# linux 文本操作

> 主要是``awk/grep/sed``这三驾马车，加上vi这个神器，最后辅助一些小工具，包括 ``wc,cat,diff,join,paste,cut,uniq``

这里 简要地整理下Linux用来处理数据文本的工具。具体命令详情请在[Linux命令大全](http://man.linuxde.net/)中搜索或者查阅其他相关资料。


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

## cut


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
gene 11869 14412
transcript 11869 14409
exon 11869 12227
exon 12613 12721
exon 13221 14409
transcript 11872 14412
exon 11872 12227
exon 12613 12721
exon 13225 14412
transcript 11874 14409
```
注意，使用这个命令是为了好观察，不要把用它处理然后把结果传入文本（会导致程序处理文件效率降低，因为文本解析速度会下降）。

`cut`和`column`默认以`\t`为分隔符，这里也能够用`-s`选项指定。

先把之前的tab分隔文件弄成逗号分隔文件，然后使用`-s`选项看看：
```shell
wsx@wsx-ubuntu:~/Work/research/Promoter_Research`$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5 | awk '{FS="\t";OFS=",";}{print $`1,`$2,$`3}'
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

wsx@wsx-ubuntu:~/Work/research/Promoter_Research`$ grep -v "^#" Homo_sapiens.GRCh37.75.gtf | head -n 10 | cut -f 3-5 | awk '{FS="\t";OFS=",";}{print $`1,`$2,$`3}'| column -s "," -t
gene 11869 14412
transcript 11869 14409
exon 11869 12227
exon 12613 12721
exon 13221 14409
transcript 11872 14412
exon 11872 12227
exon 12613 12721
exon 13225 14412
transcript 11874 14409
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
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 example_sorted.bed example_length.txt > example_with_length.txt
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
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 example_sorted.bed example_length_alt.txt chr1 26 39 53453
chr1 32 47 53453
chr1 40 49 53453
chr1 9 28 53453
chr2 35 53 34356
```
如果第二个文件没有`chr3`，`join`之后也没了！！

我们可以通过`-a`选项指定哪一个文件可以不遵循配对
```shell
wsx@wsx-ubuntu:/tmp$ join -1 1 -2 1 -a 1 example_sorted.bed example_length_alt.txt
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
wsx@wsx-ubuntu:/tmp`$ awk '{ print $`2 $3}' example.bed
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



## 用sed进行流编辑

sed工作流：读取`$\to$`执行命令`$\to$`显示。默认情况，所有的命令都会一个叫做在模式空间（pattern buffer）的缓冲区进行。因此不会改变原始输入文件的内容。


### 语法规则

sed即支持在命令行中用单引号输入执行命令，也支持执行含有sed命令的文件。使用方式如下：

```bash
sed [‐n] [‐e] 'command(s)' files
sed [‐n] ‐f script files
```
 
**选项**

sed 后面首先需要跟参数，支持的参数有：

选项与参数：
-n ：禁止显示所有输入内容，只显示经过sed处理的行(常用)
-e ：直接在命令列模式上进行 sed 的动作编辑，接要执行的一个或者多个命令
-f ：执行含有sed 动作的文件
-r ：sed 的动作支持的扩展正则(默认基础正则)
-i ：直接修改读取的文件内容，不输出。

### 作用区域

默认情况下，sed命令会作用于文本数据的所有行。如果只想作用于某些行时，则需要使用在命令通过**行号**或者**文本过滤**的方式前指明作用区域。

#### 行号

使用数字行号时，类似于R中的向量子集提取。单独数字表示某一行，逗号分割指示行范围，另外`m,+n`表示从m行开始向下n行，`m~n`表示从m行开始的每n行。

例如`sed ‐n '2~2 p' test.txt` 输出偶数行，`sed ‐n '1~2 p' test.txt`输出奇数行内容。

#### 文本过滤

`'/pattern/ command' `可以只在包含pattern 的行中执行命令。如`sed ‐n '/hello/ p' test.txt`只会打印出包含hello的行。`sed ‐n '/hello/, /world/ p' test.txt` 打印两者之间的所有行。

特殊情况下也可以将文本过滤和行号结合使用，`sed ‐n '/hello/，+5 p' test.txt` 打印第一次出现hello的下面5行

### 命令

**p 复制**

复制模式空间中的内容，如果不和`-n`参数连用，每一行都会在屏幕输出两次，一行正常输出一行复制，结合`-n` 参数后就可以打印需要的内容。

**d 删除**

没什么可以说的，支持按照行号或者匹配来删除。


**i 插入**

有的时候一个结果文件没有header，使用sed 可以轻松完成。在匹配位置之前插入内容。

```bash
sed '1i name\tlength\foldchange' test.txt
```

**a 追加**

和插入命令的区别在于在匹配位置后一行插入内容，，如果想在末尾插入一行信息时将$作为地址。

```bash
sed '$a auther:zhaofei' test.txt
```
**c 行替换**

有了行的删除插入和追加自然也就会有行替换。

```bash
sed '$c auther:zhaofei' test.txt
```

**y 字符转换**

sed中的y命令可以实现**一一映射的字符**替换（注意和s命令的区别）。

```bash
[address]y/inchars/outchars/
# inchars中的第一个字符会被转换为outchars中的第一个字符，第二个字符会被转换成outchars中的第二个字符
# 直持续到处理完指定字符。如果inchars和outchars的长度不同会报错
```
**l 输出隐藏字符**

类似与cat -A，但是显示隐藏字符形式不同。

**w 写入新文件**

增强版的cp，只复制自己想要的东西，也可以将一个文件按不同的筛选条件分开保存。

```bash
sed -n -e '/a/ w a.txt' -e '1,10 w b.txt' test.txt
sed '1,10d;w new.txt' test.txt
```

**r 读取文件**

如果现在a文件的某个地方插入b文件,如在第三行插入

```bash
sed '3r b.txt' a.txt
```

**e 执行外部命令**

首先要区别于参数-e,这个e是在''里面的命令

```bash
sed 'e echo "hello"' test.txt
```

**i 反向执行**

在命令前加!反向执行命令

```bash
sed '/hello/!d' test.txt

#效果和下面的命令一致

sed -n '/hello/p' test.txt
```

**n 匹配行下移一行操作**

提前读取当前行的下一行内容，并且覆盖当前模式空间中的行

```bash
seq 5 |sed '3{n;d}'

# 首先匹配到第三行，然后移动到第四行进行删除
# 所及结果是
1
2
3
5

seq 5 |sed 'n;d' #效果类似与输出奇数行
seq 5 |sed -n 'n;p' #效果类似与输出偶数行

```

**= 打印行号**

```bash
sed '/hello/!d;=' test.txt
```

**s 替换**

通用写法`[address1[,address2]]s/pattern/replacement/[flags]`

这里pattern部分支持正则匹配，flags包括n（替换第n个匹配项），g（全局替换），p（输出改变的行，结合-n）,i（忽略大小写匹配），w（保存改变的行到新文件）。

如果要替换的的内容包括了`/`，第一种方式是使用`\/`进行转义，第二种方法是使用`@ | ! ^`作为分隔符。

有时候我们会对文件中的目录进行替换，可以下面的写法

```
pwd |sed 's@/home/zhaofei@/home/feizhao@'
pwd |sed 's^/home/zhaofei^/home/feizhao^'
pwd |sed 's|/home/zhaofei|/home/feizhao|'
```

pattern支持各种正则表示法，例如

```bash
^ 行首
$ 行尾
. 换行符之外的任意单个字符
? 匹配之前项0次或者一次
+ 匹配1次或者多次
* 匹配0次或者多次
{n} 匹配n次
{n,} 匹配至少n次
{m,n} 至少m,最多n
[] 匹配任意一个
[-] 匹配范围中的一个
[^] 排除字符
| 或者

# posix字符类

[:alnum:] 字母数字
[:alpha:] 字母
[:blank:] 空格制表符
[:digit:] 数字
[:lower:] 小写字母
[:upper:] 大写字母
[:punct:] 标点
[:space:] 所有空白符（换行符和回车）

# 元字符
\s 单个空白
\w 单词

```

在进行匹配替换时，我们有时候并不想删除匹配的内容，只是希望其以另一种形式和替换内容一起出现。在sed中，特殊字符`&`用来存储匹配模式中的内容。

例如
```bash
sed 's/[[:digit:]]/number = &/ test.txt
```

### sed 单行命令

```bash
# 删除空行
sed '/^$/d'

#每行后增加空行
sed G

# 在每5行后增加一空白行
gsed '0~5G'


# 在匹配式样“regex”的行之后插入一空行
sed '/regex/G'

# 在匹配式样“regex”的行之前和之后各插入一空行
sed '/regex/{x;p;x;G;}'

# 过滤所有的html标签
sed 's/<[^>]*>//g ; /^$/d' html.txt

# cat

sed ''

# head

sed '10 q'

# Dos2unix

sed 's/^M$//'

# Unix2dos

sed 's/$/\r/'

# nl(添加行号)
# sed行号会独占一行
sed = input.file | sed 'N;s/\n/\t/'

# tee

sed ‐n 'p; w new.txt'

# grep

sed ‐n '/pattern/p'

# grep -v

sed ‐n '/pattern/p!'

# 计算行数

sed -n '$='

# 多个内容同时替换

sed 's/a\|b\|c/d/' tmp.txt

# 将每两行连接成一行

sed '$!N;s/\n/ /'

# 如果当前行以等号开头，将当前行并到上一行末尾
# 并以单个空格代替原来行头的“=”
sed -e :a -e '$!N;s/\n=/ /;ta' -e 'P;D'

# 显示包含“AAA”、“BBB”或“CCC”的行（任意次序）
sed '/AAA/!d; /BBB/!d; /CCC/!d'

```

本章节作者：思考问题的熊 + **DivinerSX**