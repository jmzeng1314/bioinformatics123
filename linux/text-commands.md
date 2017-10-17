# linux 文本操作

主要是``awk/grep/sed``这三驾马车，加上vi这个神器，最后辅助一些小工具，包括 ``wc,cat,diff,join,paste,cut,uniq`` 




测试一下。
---

参考学习《Bioinformatics. Data. Skills》，这里简要地整理下Linux用来处理数据文本的工具。具体命令详情请在[Linux命令大全](http://man.linuxde.net/)中搜索或者查阅其他相关资料。


`head`,`tail`查看文档头尾。`-n`选项可以指定行数。

`less`用来查阅文档，`q`退出，`space bar`翻页，`g`第一行，`G`最后一行，`j`下，`k`上,`/<pattern>`往下搜索模式，`?<pattern>`往上搜索模式，`n`前一个匹配字符，`N`后一个匹配字符。

`less`可以用于debug，查看中间输出结果。比如
```shell
step1 input.txt | step2 | step3 > output.txt
# step1,2,3为程序或命令名
```
可以写为
```shell
step1 input.txt | less
step1 input.txt | step2 | less
step1 input.txt | step2 | step3 | less
```

## 纯文本信息汇总

`wc`命令默认依次输出单词数、行数、总字符数。查看行数使用`wc -l`。
如果存在空行，空行会被计数。可以使用`grep`命令实现非空行计数
`grep -c "[^ \\n\\t]" some_data.bed`

`ls -lh`以易读形式查看文件大小。

输出文件列数：
```shell
# -F指定分隔符，此处假定是table键分隔，默认空格键
awk -F "\t" '{print NF; exit}' some_data.bed
```

### 怎么去除注释的元数据行呢？怎么计数非注释行行数呢？

可以使用`tail`结合`awk`，试试gtf(基因组注释文件)
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ head -n 6 Homo_sapiens.GRCh37.75.gtf 
#!genome-build GRCh37.p13
#!genome-version GRCh37
#!genome-date 2009-02
#!genome-build-accession NCBI:GCA_000001405.14
#!genebuild-last-updated 2013-09
1	pseudogene	gene	11869	14412	.	+	.	gene_id "ENSG00000223972"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene";
```
可以看到注释行是5行，我们利用`tail`试试去掉它
```shell
# 注意此处 -n后接的"+"号
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ tail -n +5 Homo_sapiens.GRCh37.75.gtf | head -n 1
#!genebuild-last-updated 2013-09
```
发现还有一行没去掉
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ tail -n +6 Homo_sapiens.GRCh37.75.gtf | head -n 1
1	pseudogene	gene	11869	14412	.	+	.	gene_id "ENSG00000223972"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene";
```
成功搞定，然后结合前面提到的`awk`命令即可计算行数。

上面方法鲁棒性不够（人为地确定行数），一种更为通用的方法是`grep`结合`awk`命令
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 1
1	pseudogene	gene	11869	14412	.	+	.	gene_id "ENSG00000223972"; gene_name "DDX11L1"; gene_source "ensembl_havana"; gene_biotype "pseudogene";
```
推荐使用这种。

## Cut


`cut`可以处理列数据，`-f`选项指定列，可以是一个范围（比如2-8），注意不能用它给列排序。
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3
gene
transcript
exon
exon
exon
transcript
exon
exon
exon
transcript
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5
gene	11869	14412
transcript	11869	14409
exon	11869	12227
exon	12613	12721
exon	13221	14409
transcript	11872	14412
exon	11872	12227
exon	12613	12721
exon	13225	14412
transcript	11874	14409
```
`-d`选项可以指定分隔符，比如`-d,`指定`,`为分隔符。

使用`column`命令来格式化输出，上次的命令结果输出明显没对齐，我们把它对齐看看：
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5 | column -t
gene        11869  14412
transcript  11869  14409
exon        11869  12227
exon        12613  12721
exon        13221  14409
transcript  11872  14412
exon        11872  12227
exon        12613  12721
exon        13225  14412
transcript  11874  14409
```
注意，使用这个命令是为了好观察，不要把用它处理然后把结果传入文本（会导致程序处理文件效率降低，因为文本解析速度会下降）。

`cut`和`column`默认以`\t`为分隔符，这里也能够用`-s`选项指定。

先把之前的tab分隔文件弄成逗号分隔文件，然后使用`-s`选项看看：
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research`$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5 |  awk '{FS="\t";OFS=",";}{print $`1,`$2,$`3}'
gene,11869,14412
transcript,11869,14409
exon,11869,12227
exon,12613,12721
exon,13221,14409
transcript,11872,14412
exon,11872,12227
exon,12613,12721
exon,13225,14412
transcript,11874,14409

wsx@wsx-ubuntu:~/Work/research/Promoter_Research`$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5 |  awk '{FS="\t";OFS=",";}{print $`1,`$2,$`3}'| column -s "," -t
gene        11869  14412
transcript  11869  14409
exon        11869  12227
exon        12613  12721
exon        13221  14409
transcript  11872  14412
exon        11872  12227
exon        12613  12721
exon        13225  14412
transcript  11874  14409
```

## grep

`grep`处理速度非常之快，能用它尽量用它。`--color=auto`可以激活颜色（标记匹配文字），更方便查看。

`-v`选项排除匹配到的，`-w`进行完全匹配。这样可以防止，你想排除`abc`结果把`abc1`，`abcd`也排除掉了。

`-B`指定输出包括匹配到的前多少行，比如`-B1`就是前一行；`-A`指定输出包括匹配到的后多少行，比如`-A2`就是包括了后两行。`-C`指定输出包括匹配到的前后多少行。
`grep`支持基本正则表达式，`-E`指定支持扩展表达式，或者用`egrep`命令。
`-c`选项对匹配的行计数；`-o`选项只抽离输出匹配的部分
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -E -o 'gene_id "\w+"' Homo_sapiens.GRCh37.75.gtf | head -n 5
gene_id "ENSG00000223972"
gene_id "ENSG00000223972"
gene_id "ENSG00000223972"
gene_id "ENSG00000223972"
gene_id "ENSG00000223972"
```
发现冗余项非常多，如果我们只要唯一的呢，怎么办？
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research$ grep -E -o 'gene_id "(\w+)"' Homo_sapiens.GRCh37.75.gtf | cut -f2 -d" "| sed 's/"//g' | sort | uniq | head -n 10
ENSG00000000003
ENSG00000000005
ENSG00000000419
ENSG00000000457
ENSG00000000460
ENSG00000000938
ENSG00000000971
ENSG00000001036
ENSG00000001084
ENSG00000001167
```
虽然我的笔记本呼啦啦作响，但是还是非常快就跑完了。



## file查看文件编码

```shell
wsx@wsx-ubuntu:~$ file regular_express.txt 
regular_express.txt: ASCII text, with CRLF, LF line terminators
```
常用的大型数据文件一般存为ASCII码形式（像几大基因bank的数据文件），而我们自己认为创建的常为UTF-8，所以有时候认为处理文件需要会碰到把UTF-8编码的字符插入到ASCII码文件里去了。遇到这种问题，我们可以用`hexdump -c`命令查看出错的地方（手边没有这样的文件，就不举例了）。

## 用sort对文本排序

我们先创建一个bed格式文件来试试这个命令：
```shell
wsx@wsx-ubuntu:~$ cat test.bed 
chr1	26	39
chr3	32	47
chr1	40	50
chr1	9	28
chr2	35	54
chr1	10	19
wsx@wsx-ubuntu:~$ sort test.bed 
chr1	10	19
chr1	26	39
chr1	40	50
chr1	9	28
chr2	35	54
chr3	32	47

```
可以明显看到文本按照第一列进行了排序。
默认，`sort`用空格或tab键作为域（列）分隔符。如果我们用其他形式的分隔符，需要用`-t`选项指定。


下面是对`bed`文件最通用的排序命令：
```shell
wsx@wsx-ubuntu:~$ sort -k1,1 -k2,2n test.bed 
chr1	9	28
chr1	10	19
chr1	26	39
chr1	40	50
chr2	35	54
chr3	32	47
```
基本操作`bedtools`软件都会先用这个命令对`bedtools`文件排序。
现在略加解释一下，`sort`用`-k`选项指定某列的排序方式。而每次使用`-k`选项都要带上指定列的范围(start, end)。如果只指定一列，就为(start,start)了，像上面命令的`-k1,1`就是。也许你会觉得`-k2,2n`很奇怪，这里的`n`指定程序把第二列当做数值对待。如果不做设定，都是当做字符对待（shell都是这么对待数值数据的）。所以总结其他这一行命令就是对第一列按照字符排序，第二列按照数值排序。

我们可以用`-c`选项检查一个文件是不是已经按照过某种方式排过序了。
```shell
wsx@wsx-ubuntu:~$ sort -k1,1 -k2,2n test.bed | sort -k1,1 -k2,2 -c
sort：-:2：无序： chr1	10	19
wsx@wsx-ubuntu:~`$ echo $`?
1
wsx@wsx-ubuntu:~$ sort -k1,1 -k2,2n test.bed | sort -k1,1 -k2,2n -c
wsx@wsx-ubuntu:~`$ echo $`?
0

```
上面可以清楚地看到`sort`是怎么对待文件的（一般shell返回0表示成功执行）。
```shell
wsx@wsx-ubuntu:~$ tsfds
tsfds：未找到命令
wsx@wsx-ubuntu:~`$ echo $`?
127
wsx@wsx-ubuntu:~$ echo test
test
wsx@wsx-ubuntu:~`$ echo $`?
0
```
shell的命令退出状态码表示了该命令执行的完成的某种情况。不同的状态码有不同的含义，具体可以百度查阅（我之前整理的shell笔记应该讲过，可以看看）。

反向排序用`-r`选项。如果你只想反转一列，可以把它加在`-k`选项后。

```shell
wsx@wsx-ubuntu:~$ sort -k1,1 -k2,2nr test.bed 
chr1	40	50
chr1	26	39
chr1	10	19
chr1	9	28
chr2	35	54
chr3	32	47
```
现在我给`test.bed`加一行：
```shell
wsx@wsx-ubuntu:~$ cat test.bed 
chr1	26	39
chr3	32	47
chr1	40	50
chr1	9	28
chr2	35	54
chr1	10	19
chr11	22	56
```
你会发现有点奇怪
```shell
wsx@wsx-ubuntu:~$ sort -k1,1 -k2,2n test.bed 
chr1	9	28
chr1	10	19
chr1	26	39
chr1	40	50
chr11	22	56
chr2	35	54
chr3	32	47
```
怎么`chr11`在`chr2`前面？其实`sort`排序的方式有点像查字典。例子中，命令先比较`c`，然后比较`h`，然后比较`r`，接着比较`1`，自然`11`会在`2`前面了。这里可以添加`V`选项修改。
```shell
wsx@wsx-ubuntu:~$ sort -k1,1V -k2,2n test.bed 
chr1	9	28
chr1	10	19
chr1	26	39
chr1	40	50
chr2	35	54
chr3	32	47
chr11	22	56
```
是不是觉得这样更可观一些？不过通常在处理数据时不做此处理，符合 规范的数据可以让后续处理程序效率更高。

基本掌握`sort`这些也够用了，它主要为后续处理服务。如果想知道其他的用法，查查吧，同时欢迎发文来交流。

## 用uniq寻找唯一值

首先创建样例文本
```shell
wsx@wsx-ubuntu:~$ cat test.letter 
A
A
B
C
B
C
C
C
D
F
D
```
使用`uniq`看看
```shell
wsx@wsx-ubuntu:~$ uniq test.letter 
A
B
C
B
C
D
F
D
```
尼玛，怎么不对。它好像只去掉了连续的同一字符。怎么办？想想我们刚学了什么命令？`sort`不是刚好可以把同样的字符弄到一起去吗，然后再使用`uniq`，嘿嘿：
```shell
wsx@wsx-ubuntu:~$ sort test.letter | uniq
A
B
C
D
F
```
哟呵，got you。

加`-c`选项计数：
```shell
wsx@wsx-ubuntu:~$ sort test.letter | uniq -c
      2 A
      2 B
      4 C
      2 D
      1 F
```
把结果再排序
```shell
wsx@wsx-ubuntu:~$ sort test.letter | uniq -c | sort -rn
      4 C
      2 D
      2 B
      2 A
      1 F
```
`-d`选项只输出重复行
```shell
wsx@wsx-ubuntu:~$ cat test.letter 
A
A
B
C
B
C
C
C
D
F
D
wsx@wsx-ubuntu:~$ uniq -d test.letter 
A
C
wsx@wsx-ubuntu:~$ sort test.letter | uniq -d
A
B
C
D

```
使用时需要注意处理不同导致的结果差异。

## Join 命令

用来连接文件。
假设现在我们有两个文件：

```shell
wsx@wsx-ubuntu:/tmp$ cat example.bed 
chr1	26	39
chr1	32	47
chr3	11	28
chr1	40	49
chr3	16	27
chr1	9	28
chr2	35	53
wsx@wsx-ubuntu:/tmp$ cat example_length.txt 
chr1	53453
chr2	34356
chr3	24356
```
我想把第二个文件说明染色体长度添加到第一个文件对应染色体的第三列。
我们首先要给文件排序（使用`join`前必须做），然后使用`join`命令。
```shell
wsx@wsx-ubuntu:/tmp$ sort -k1,1 example.bed > example_sorted.bed
wsx@wsx-ubuntu:/tmp$ sort -c -k1,1 example_length.txt 
wsx@wsx-ubuntu:/tmp$ cat example_sorted.bed 
chr1	26	39
chr1	32	47
chr1	40	49
chr1	9	28
chr2	35	53
chr3	11	28
chr3	16	27
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 example_sorted.bed  example_length.txt > example_with_length.txt
wsx@wsx-ubuntu:/tmp$ cat example_with_length.txt 
chr1 26 39 53453
chr1 32 47 53453
chr1 40 49 53453
chr1 9 28 53453
chr2 35 53 34356
chr3 11 28 24356
chr3 16 27 24356
```
命令基本语法是
```
join -1 <file_1_field> -2 <file_2_field> <file_1> <file_2>
```
既然名字叫`join`，就是两者必须有共同之处，通过共同的支点将两者连为一体。
`-1`和`-2`选项后接参数分别指定了这个支点，也就是连接的域（列）。比如例子中，都是两个文件的第一列。

两个文件中，第一列都共有`chr1(2)(3)`。 如果不一致会出现什么情况呢？

```shell
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 example_sorted.bed  example_length_alt.txt chr1 26 39 53453
chr1 32 47 53453
chr1 40 49 53453
chr1 9 28 53453
chr2 35 53 34356
```
如果第二个文件没有`chr3`，`join`之后也没了！！

我们可以通过`-a`选项指定哪一个文件可以不遵循配对
```shell
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 -a 1 example_sorted.bed  example_length_alt.txt 
chr1 26 39 53453
chr1 32 47 53453
chr1 40 49 53453
chr1 9 28 53453
chr2 35 53 34356
chr3 11 28
chr3 16 27
```

## awk

`awk`是文本处理的一把好手，虽然它不能像`python`，`R`干一些高级复杂的主题工作，但是它具备完整的命令操作和编程体系。

`awk`是一门语言，我不可能在学习的时候能够逻辑清晰详细地介绍给大家。主要是还通过实例来了解用法，加深认识。手册可以参考http://man.linuxde.net/awk。

首先要明白的是，`awk`按行处理数据。在shell知识里，如果把一个文档看做一张表。那么一行就是一个**记录**，一列就是一个**域**。可以看出，`awk`就是按记录处理文本的。

其次是`awk`的程序结构是
```
pattern {action}
```
pattern可以是表达式或者正则表达式。pattern有点像`if`语句，当它满足时就会执行相应的动作。

另一个`awk`核心是它用`$0`表示所有列，`$1`，`$2`...等等表示对应的列。我们可以很方便地用它进行操作。
```shell
wsx@wsx-ubuntu:/tmp`$ awk '{print $`0}' example.bed 
chr1	26	39
chr1	32	47
chr3	11	28
chr1	40	49
chr3	16	27
chr1	9	28
chr2	35	53
wsx@wsx-ubuntu:/tmp`$ awk '{print $`1}' example.bed 
chr1
chr1
chr3
chr1
chr3
chr1
chr2
wsx@wsx-ubuntu:/tmp`$ awk '{print $`2}' example.bed 
26
32
11
40
16
9
```

`print`语句就像动作一样输出你操作的结果。
```shell
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`2 "\t" $3}' example.bed 
26	39
32	47
11	28
40	49
16	27
9	28
35	53
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`2  $3}' example.bed 
2639
3247
1128
4049
1627
928
3553
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`2 , $3}' example.bed 
26 39
32 47
11 28
40 49
16 27
9 28
35 53

```
了解上述几个语句的不同。

表示染色体名一般用带`chr`或者不带`chr`标志两种方式。当我们要用到这两种时，肯定要让它们能够对应起来，也就是转换。`awk`命令可以非常方便地添加`chr`标记。

下面我先把例子文件的`chr`去掉，然后加上试试。
```shell
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`1}' example.bed
chr1
chr1
chr3
chr1
chr3
chr1
chr2
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`1}' example.bed | cut -c4 
1
1
3
1
3
1
2
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`1}' example.bed | cut -c4 | awk '{print "chr"$1}'
chr1
chr1
chr3
chr1
chr3
chr1
chr2
```

`awk`作为一门编程语言，它支持各种操作符（运算，逻辑，判断）喔。
```shell
wsx@wsx-ubuntu:/tmp`$ awk '$`3 - $2 >18' example.bed 
chr1	9	28
wsx@wsx-ubuntu:/tmp`$ awk '$`1 ~/chr1/ && `$3 - $`2 > 10' example.bed 
chr1	26	39
chr1	32	47
chr1	9	28

# 这里 ~ 符号用来匹配正则表达式
```
还有`awk`存在一些变量，像`NR`表示行号，`OFS`表示输出分隔符等。
```shell
wsx@wsx-ubuntu:/tmp$ awk 'NR >= 3 && NR <= 5' example.bed 
chr3	11	28
chr1	40	49
chr3	16	27
```

如果我们想把`gtf`文件转换成为`bed`格式，可以使用
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research`$ head -n1000 Homo_sapiens.GRCh37.75.gtf | awk '!/^#/{ print $`1 "\t" `$4-1 "\t" $`5} ' | head -n 3
1	11868	14412
1	11868	14409
1	11868	12227
```
因为篇幅有限，我不可能输出所有结果，所以只取部分数据做了运算。



## 用Sed进行流编辑

`sed`命令从文本或者标准输入中每次读入一行数据。

我们先从简单的实例出发，看下该命令怎么将一列中的`chrm12`,`chrom2`等转换成`chr12`，`chr2`的格式。

```shell
wangsx@SC-201708020022:~/tmp$ cat chrms.txt
chrom1    3214482    3216968
chrom1    3214234    3216968
chrom1    3213425    3210653

wangsx@SC-201708020022:~/tmp$ sed 's/chrom/chr/' chrms.txt
chr1    3214482    3216968
chr1    3214234    3216968
chr1    3213425    3210653
```

虽然示例文件处理仅仅只有三行，但我们可以将这种处理方式运用到上G甚至更大的数据文件中，而不用打开整个文件进行处理。并且，可以借助重导向实现对数据处理结果的输出。

`sed`替换命令采用的格式是

```shell
s/pattern/replacement/
```

`sed`会自动搜索符合`pattern`的字符串，然后修改为`replacement`（我们想要修改后的样子）。一般默认`sed`只替换第一个匹配的`pattern`，我们可以通过添加全局标识`g`将其应用到数据的所有行中。

```shell
s/pattern/replacement/g
```

如果我们想要忽略匹配的大小写，使用`i`标识

```shell
s/pattern/replacement/i
```

默认`sed`命令支持基本的POSIX正则表达式（BRE），可以通过`-E`选项进行拓展（ERE）。很多的Linux命令都这种方式，像常用的`grep`命令。



再看一个实例，如果我们想把`chr1:28647389-28659480`这样格式的文字转换为三列，可以使用：

```shell
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" | \
>  sed -E 's/^(chr[^:]+):([0-9]+)-([0-9]+)/\1\t\2\t\3/'
chr1    28647389        28659480
```

我们聚焦在第二个命令`sed`上。初看杂乱无章，但是从最大的结构看依旧是

```shell
s/pattern/replacement/
```

先看`pattern`部分，这是由几个简单正则表达式组成的复合体，几个`()`括起来的字符串可以单独看。第一个匹配`chr`加上一个非冒号的字符，第二个和第三个都是匹配多个数字。最开始的`^`表示以`chr`起始（前面没有字符），各个括号中间的是对应的字符。整体的`pattern`的目的就是为了找到文本中符合这种模式的字符串，如果只是想把这个模式找出来的话，几个括号可以不用加。显然这几个括号的作用就是将它们划分成多个域，帮助`sed`进行处理。可以看到`replacement`部分存在`\1`,`\2`,`\3`，它恰好对应`()`的顺序。这样我们在中间插入`\t`制表符，就可以完成我们想要的功能：将原字符串转换为三列。

我本身对字符串并不是非常熟悉，懂一些元字符，可能讲解的不是很到位。不熟悉正则表达式的朋友，可以学习和参考下[学习正则表达式](http://www.jianshu.com/p/7c50954651fa)，是我从Github上Copy到的非常好的学习资料，有兴趣也可以Fork学习。

上山的路总是有很多条，我们下面看下其他实现该功能的办法：

```shell
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  sed 's/[:-]/\t/g'
chr1    28647389        28659480
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  sed 's/:/\t/' | sed 's/-/\t/'
chr1    28647389        28659480
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  tr ':-' '\t'
chr1    28647389        28659480
```

这三种方式看起来都非常简单有效。它处理字符串的思路不是从匹配pattern然后替换入手，不对，应该说是不是从匹配所有pattern然后替换入手。处理的关键是只处理字符串中看似无用的连字符`:`与`-`，将其替换成制表符从而轻松完成分割。

` sed 's/:/\t/' | sed 's/-/\t/'`可以通过`-e`选项写为` sed -e 's/:/\t/' -e 's/-/\t/'`，效果等价。


默认`sed`命令支持基本的POSIX正则表达式（BRE），可以通过`-E`选项进行拓展（ERE）。很多的Linux命令都这种方式，像常用的`grep`命令。



再看一个实例，如果我们想把`chr1:28647389-28659480`这样格式的文字转换为三列，可以使用：

```shell
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" | \
>  sed -E 's/^(chr[^:]+):([0-9]+)-([0-9]+)/\1\t\2\t\3/'
chr1    28647389        28659480
```

我们聚焦在第二个命令`sed`上。初看杂乱无章，但是从最大的结构看依旧是

```shell
s/pattern/replacement/
```

先看`pattern`部分，这是由几个简单正则表达式组成的复合体，几个`()`括起来的字符串可以单独看。第一个匹配`chr`加上一个非冒号的字符，第二个和第三个都是匹配多个数字。最开始的`^`表示以`chr`起始（前面没有字符），各个括号中间的是对应的字符。整体的`pattern`的目的就是为了找到文本中符合这种模式的字符串，如果只是想把这个模式找出来的话，几个括号可以不用加。显然这几个括号的作用就是将它们划分成多个域，帮助`sed`进行处理。可以看到`replacement`部分存在`\1`,`\2`,`\3`，它恰好对应`()`的顺序。这样我们在中间插入`\t`制表符，就可以完成我们想要的功能：将原字符串转换为三列。

我本身对字符串并不是非常熟悉，懂一些元字符，可能讲解的不是很到位。不熟悉正则表达式的朋友，可以学习和参考下[学习正则表达式](http://www.jianshu.com/p/7c50954651fa)，是我从Github上Copy到的非常好的学习资料，有兴趣也可以Fork学习。

上山的路总是有很多条，我们下面看下其他实现该功能的办法：

```shell
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  sed 's/[:-]/\t/g'
chr1    28647389        28659480
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  sed 's/:/\t/' | sed 's/-/\t/'
chr1    28647389        28659480
wangsx@SC-201708020022:~/tmp$ echo "chr1:28647389-28659480" |  tr ':-' '\t'
chr1    28647389        28659480
```

这三种方式看起来都非常简单有效。它处理字符串的思路不是从匹配pattern然后替换入手，不对，应该说是不是从匹配所有pattern然后替换入手。处理的关键是只处理字符串中看似无用的连字符`:`与`-`，将其替换成制表符从而轻松完成分割。

` sed 's/:/\t/' | sed 's/-/\t/'`可以通过`-e`选项写为` sed -e 's/:/\t/' -e 's/-/\t/'`，效果等价。



默认，`sed`会输出每一行的结果，用`replacement`替换`pattern`，但实际中我们可能会因此得到不想要的结果。比如下面的这个例子。

如果我们想要抓出`gtf`文件第九列的转录名，可能会使用以下命令

```shell
wangsx@SC-201708020022:~/database$ zgrep -v "^#" gencode.v27lift37.annotation.gtf.gz | head -n 3 | \
> sed -E 's/.*transcript_id "([^"]+)".*/\1/'
chr1    HAVANA  gene    11869   14409   .       +       .       gene_id "ENSG00000223972.5_2"; gene_type "transcribed_unprocessed_pseudogene"; gene_name "DDX11L1"; level 2; havana_gene "OTTHUMG00000000961.2_2"; remap_status "full_contig"; remap_num_mappings 1; remap_target_status "overlap";
ENST00000456328.2_1
ENST00000456328.2_1
```

我们可以发现一些没有转录名行的结果是输出整行，这可不是我们想要的。一种解决办法是在使用`sed`之前先抓出有`transcript_id`的行。其实`sed`命令本身也可以通过选项和参数设定解决这个问题，这里我们可以用`-n`选项关闭`sed`输出所有行，在最末的`/`后加`p`只输出匹配项。

```shell
wangsx@SC-201708020022:~/database$ zgrep -v "^#" gencode.v27lift37.annotation.gtf.gz | head -n 3 | sed -E -n 's/.*transc
ript_id "([^"]+)".*/\1/p'
ENST00000456328.2_1
ENST00000456328.2_1
```

注意方括号内`^`是非（取反）的意思。

解释如下：

1. 首先，匹配字符串"transcript_id"之前0或多个任意字符（`.`表示除换行键的任意字符）。
2. 然后，匹配和捕获一个或多个不是引号的字符，用的是`[^"]`

`+`号的使用是一种非贪婪的方法。很多新手会用`*`，这是贪婪操作，往往会得不偿失，需要注意喔。

```shell
wangsx@SC-201708020022:~/database$ sed -E 's/transcript_id "([^"]+)".*/\1/' greedy_example.txt
ENSMUST00000160944
wangsx@SC-201708020022:~/database$ sed -E 's/transcript_id "(.*)".*/\1/' greedy_example.txt
ENSMUST00000160944"; gene_name "Gm16088
```

使用`*`时它会尽量多地去匹配符合要求的模式。



我们也可以用`sed`命令来获取特定范围的行，比如说我要取出头10行，可以使用

```shell
sed -n '1,10p' filename
```

20到50行

```shell
sed -n '20,50p' filename
```

当然`sed`的功能特性远远不止这些，有待于大家更多地挖掘。不过需要注意的是，尽量让工具干它最擅长的事情。如果是复杂地大规模计算，还是最好写个Python脚本。

> **KISS原则**: 
>
> Keep Incredible Sed Simple


##