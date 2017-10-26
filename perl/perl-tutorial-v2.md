---
[TOC]
### 1. Perl生信编程课程内容
#### 1.1. 推荐入门资料
- Perl语言入门（第六版)-小骆驼 _（适合当作案头工具书，建议通读一遍）_
- Perl菜鸟教程：http://www.runoob.com/perl/perl-tutorial.html _（简洁的菜鸟教程，结合小骆驼一起使用）_
- Perl帮助文档perldoc：http://perldoc.perl.org/perl.html _(Perl自带的数千页文档）_
- Beginning Perl for Bioinformatics_（简单翻阅，以本课程的实战题为主）_
- 基因帮Perl讲义(四部分)：数据结构介绍 ->控制程序流及文本处理 ->正则匹配与复杂数据结构->高效编程_（用Perl单行命令演示，不太适合初学者。）_
- Perl单行命令_（简单翻阅，尝试实现简单的数据转换任务）_

#### 1.2. 划定学习范围和重点

**大致包括：**
开发环境的搭建；
编写Perl程序的方法；
良好的编程习惯；
生信相关的知识要点：[如何系统入门Perl-Jimmy总结](http://mp.weixin.qq.com/s/lq6pSGtbZxbc0A6-ZLqaqQ) 

![如何系统入门Perl](./image/summary.PNG)

#### 1.3.  课程目标

![summary](./image/1a61e850e1efcb28e66975303725ee3c.png)

本课程的**目标**是讲解结束后，Perl基础编程语法入门，讲解以下部分编程题，学员能够独立完成剩余题目：
##### 对FASTQ的操作
- 5,3段截掉几个碱基 
- 序列长度分布统计
- FASTQ 转换成 FASTA 
- 统计碱基个数及GC%  
##### 对FASTA的操作
- 取互补序列 
- 取反向序列
- DNA to RNA 
- 大小写字母形式输出
- 每行指定长度输出序列（格式化）
- 按照序列长度/名字排序
- 提取指定ID的序列 
- 随机抽取序列
- 去格式化
- 根据id列表获取序列
- 打乱fasta文件顺序
- 过滤小于500的序列

#### 1.4. 指导实战练习
本次课程以后，自行练习以下编程实战题：
- 1：人类基因组的外显子区域的长度
- 2：hg19基因组序列的一些探究
- 3：hg38每条染色体的基因、转录本分布
- 4：多个同样行列式文件的合并
- 5：根据GTF画基因的多个转录本结构
- 6：下载最新版的KEGG信息，并且解析好
- 7：写超几何分布检验
- 8：ID转换
- 9：根据指定染色体及坐标得到序列
- 10：根据指定染色体及坐标得到位置信息
- 11：把文件内容按照染色体分开写出
- 12：JSON格式数据的格式化
- 13：多个探针对应一个基因，取平均值、最大值 
- 14：把counts矩阵转换成RPKM矩阵
- 15：对有临床信息的表达矩阵批量做生存分析
- 16：对多个差异分析结果直接取交集并集
- 17：根据GTF格式的基因注释文件得到人所有基因的染色体坐标

### 2. 认识Perl和基础知识
#### 2.1. 认识Perl(了解)
Perl: Practical Extraction and Report Language (实用报表提取语言)
由 Larry Wall 在1987年创造，以实用、快速为开发目标，由于其灵活性被称为脚本语言的瑞士军刀。

Perl的理念：There's More Than One Way To Do It. (不只一种方法来做一件事)

python 的理念：There's Only One Way To Do It.

#####  Perl发展史

![Perl发展史](./image/history.bmp)


#####  Perl的优缺点

- 优点：
    语法简单容易自由灵活；
    运行速度快，Perl解释器直接对源代码程序解释执行；
    免费开源；
    正则表达式强大；
    写小脚本处理文本数据方便，单行命令替代sed、awk；
- 缺点：
    内置变量符不易记忆；
    底层社区不活跃；
    优秀模块少；
    可读性低，不适合写大程序及团队合作；

学习Perl有点类似学习一门口语。你只需要学习少量的词汇，就能享受交流（编程）的乐趣，勤加练习（读、写代码）就能彻底掌握。你可以不必要理解Perl的每一个细节就能变得高效而多产，所以今天我们不需要很多知识要点就能完成对FASTA/FASTQ文件进行数据处理的任务。

##### Perl的应用场景
- 文本处理：工程领域数据处理、生物学数据处理

- Web网站：如Yahoo、Amazon，Web页面也是文本文件，Perl有TCP/IP通信能力 

- 数据库处理：Perl语言有大量的拓展可以用来直接与各种主流数据库进行通信

- 邮件处理和FTP功能自动化

- 作图：[Circos:http://circos.ca/ ](http://circos.ca/)_（那种很酷炫但你看不懂的圈圈图）_

- 机器学习：[http://blogs.perl.org/users/sergey_kolychev/](http://blogs.perl.org/users/sergey_kolychev/)  _（内有Machine learning in Perl的几篇博文）_

- 统计

     **只有想不到的，没有Perl做不到的。**

#### 2.2. 第一个Perl程序
##### 开发环境的搭建
- 在Linux下，一般Perl都是自带的，但可能存在版本过旧的问题（一般v5.10以上），所以想要用最新版的可以自己下载安装包进行安装（参照[Perl菜鸟教程:Perl 环境安装](http://www.runoob.com/perl/perl-environment.html)）。

- 在Windows下使用，可以选择安装Active Perl或者Strawberry Perl，最后添加到环境变量。

    ![Strawberry Perl](./image/perl_install.png)
> [ActiveState Perl](http://www.activestate.com/activeperl/downloads) ActiveState offers both a free community version and a commercially supported binary distribution of Perl for Win32 and Perl for Win64.
> [Strawberry Perl](http://strawberryperl.com/): A 100% Open Source Perl for Windows that is exactly the same as Perl everywhere else; this includes using modules from [CPAN](https://www.perl.org/cpan.html), without the need for binary packages. Help is available from other Windows Perl developers on the #win32 irc channel on irc.perl.org (see [website](http://strawberryperl.com/) for access through a browser).

- git bash
  本课程入门学习主要是使用windows下的git bash开发环境_（因为平时还是习惯用windows系统，git bash就是一个虚拟的Linux环境）_。

  注意这里的`-a` `-e` `-l` `-n` `-F`的参数，后面讲Perl单行命令会用到的。

>Perl也是有IDE的，但是我们是用不到的，不推荐去研究。


#####  编写Perl程序方法
**<vi>：**
1: 在工作目录打开git bash
![gitbash open](./image/gitbash_open.gif)
2: 在命令行敲入```vi test.pl```
![vi](./image/vi_perl.png)
3：编写程序
  在编写程序之前需要知道自己的perl版本以及安装在哪里```which perl```
![which perl](./image/which_perl.png)
  ![vicmd](./image/vidoc.jpg)
  i 进入插入模式；
  ```perl
  #! /usr/bin/perl
  print "biotrainee.com";
  ```
  按esc 后，输入:x 指令，再按enter即可退出
  ![exit_vi](./image/exit_vi.png)
4: 运行程序
修改成可执行文件：```chmod 755  test.pl```或者```chmod +x test.pl```
在shell界面直接```./test.pl```或者```perl test.pl```
![perl run](./image/perlrun.gif)

**< notepad++>：**
>记住一些快捷键：
>CTRL+Q 注释/取消注释
>Ctrl+D 复制当前行
>Ctrl+L 删除当前行
>Shift+Tab 删除缩进
>Ctrl+S 保存文件
>可以参考：[Notepad++ 快捷键 大全](http://www.cnblogs.com/albert1017/archive/2012/08/09/2630405.html)

1：编写程序
![notepad](./image/perlnotepad.gif)
2：运行程序
在程序所在文件夹处运行，同vi的程序运行方法。

**<单行命令>**
![oneline](./image/oneline.gif)

**<练一练>**
编写一个程序，输出```biotrainee.com```

#### 2.3. 良好的编程习惯
##### 使用内建警告信息
- 在运行时，加上-w命令行
  ```perl -w my_program.pl```
- 程序中加上-w命令行
  ```#/usr/bin/perl -w```
- 对于perl5.6以后的版本，还可以通过warnings指令打开
```perl
#!/usr/bin/perl
use warnings;
```
>若要查看更详细的问题描述，在程序中加入：`use diagnostics;`
>自动寻找因为错误拼写造成的错误，在程序中加入：`use strict;`，同时还要在整个程序中用my 声明变量。

**<小总结>：**
**一般写程序，同时加上:**
```perl
use strict; 
use warnings;
```
>下面演示一些简单的语法知识写的小脚本就不加了，因为没有运行出结果，很容易找到问题的。

##### 使用帮助文档perldoc
帮助文档是最好的Perl学习资料，当遇到不认识的标识、函数时，应首先查询帮助文档。

1: 安装perldoc说明书```apt-get install perl-doc```
```perldoc perl```
![perldoc](./image/perldoc_help.png)
具体阅读文档的方法，参考[第一章 Perl 哲学](http://www.jianshu.com/p/98173e7af633?utm_campaign=maleskine&utm_content=note&utm_medium=pc_all_hots&utm_source=recommendation)
不知道为什么，git bash并不自带，所以推荐使用下面的网页版说明书。
2：网页版perldoc说明书[Perl Programming Documentation](http://perldoc.perl.org/perl.html) 
![perldoc](./image/perldoc.png)
>除此之外，学会使用搜索，我个人常用的搜索引擎是[全渠道搜索](http://dir.scmor.com/)，可以用里面的谷歌镜像来进行英文搜索，另外搜狗搜索可以检索微信、知乎上面的文章。

##### 注释
**<未使用注释程序示例>：**
```perl
#!/usr/bin/perl 
use strict;
use warnings;

my $usage=qq{$0 query_cover.txt acc2geneid.db acc2geneid.txt};
die "$usage\n" if scalar @ARGV!=3;
my($query_cover,$acc2geneid_db,$acc2geneid)=@ARGV;

open INPUT1,"$query_cover";
open INPUT2,"$acc2geneid_db";
open OUTPUT,">$acc2geneid";

my (@sub,%geneid2acc);
while(<INPUT2>){
    chomp($_);
    @sub=split /\t/,$_;
    $geneid2acc{$sub[1]}=$sub[0];
}
close(INPUT2);

while(<INPUT1>){
    chomp($_);
    if(exists($geneid2acc{$_})){
    print OUTPUT "$_\t$geneid2acc{$_}\n";
    }
}
close(INPUT1);
close(OUTPUT);
```
>上面的代码，大家肯定看不懂吧。即使是自己写的，如果你不在文件里写好注释，你也不知道这是干嘛的。

**<使用注释程序示例>：**
```perl
#!/usr/bin/perl 
use strict;
use warnings;

# @author: Shenmy
# @contact: mengyuanshen@126.com
# @time: 2017/9/18 20:05
# @desc: 将NCBI的Protein_ACC号转成NCBI的geneid

# 保证命令行参数个数为3，才能执行下面的代码
my $usage=qq{$0 query_cover.txt acc2geneid.db acc2geneid.txt};
die "$usage\n" if scalar @ARGV!=3;

# 获取命令行参数
my($query_cover,$acc2geneid_db,$acc2geneid)=@ARGV;

# 打开文件句柄
open INPUT1,"$query_cover";
open INPUT2,"$acc2geneid_db";
open OUTPUT,">$acc2geneid";

# 根据已有数据库，逐行读取acc2geneid.db，对数据根据\t分隔符进行拆分，构造NCBI的Protein_ACC号为键，NCBI的geneid为值的哈希表
# GeneID  protein_accession.version
# 1246500 AAD12597.1
# 1246500 O85067.1
# 1246500 NP_047184.1
# 1246501 AAD12599.1
######################################

my (@sub,%geneid2acc);
while(<INPUT2>){
  chomp($_);
  @sub=split /\t/,$_;
  $geneid2acc{$sub[1]}=$sub[0];
}
# 关闭文件句柄
close(INPUT2);

# 逐行读取存有待转换的Protein_ACC号文件，通过exists行数判断%geneid2acc哈希中是否存在以该ACC号储存的键值对，如果有就输出
#  protein_accession.version
# EEC51633.1     
# ACI65805.1      
# XP_002182338.1  
# XP_002185165.1  
###############################

while(<INPUT1>){
    chomp($_);
    if(exists($geneid2acc{$_})){
    print OUTPUT "$_\t$geneid2acc{$_}\n";
    }
}
# 关闭文件句柄
close(INPUT1);
close(OUTPUT);
```
**<注释的作用>：**
通过用自己熟悉的语言，在程序中对某些代码进行标注说明，这就是注释的作用，能够大大增强程序的可读性。

**<注释的分类：单行注释/多行注释>：**
一般都是使用#进行单行注释；
多行注释可以借助编辑器，在notepad++中的快捷键是Ctrl+Q；
![annotion](./image/annotion.gif)
也可以使用
```perl
=代码
代码
=cut
```
![annotion](./image/annotion2.gif)

##### 编程的过程
>上面的程序中，
>1：两个输入数据：第一列为geneid，第二列为protein_accession对应的数据集表；带查询/转换的protein_accession号；
>2：构思方法：根据已知的数据集构造以protein_accession为键，以geneid为值的哈希表；从文件中逐行读入，判断待查询的protein_accession号是否存在对应的键值对，如果存在的话就分别输出键和值。
>3：输出数据：第一列为protein_accession，第二列为geneid的文本文件

**<小总结>**
- 确定必需输入，比如用户提供的数据或信息；
- 对程序进行整体构思，包括程序计算输出结果的基本方法-算法；
- 决定结果的输出形式；
- 通过添加更多的细节改善整体构思。

#### 2.4. 变量
##### 变量的定义（var）
**程序就是用来处理数据的，而变量就是用来存储数据的。**
>在程序中，有时我们需要对2个数据进行求和，那么该怎样做呢？
>大家类比一下现实生活中，比如去超市买东西，往往咱们需要一个菜篮子，用来进行存储物品，等到所有的物品都购买完成后，在收银台进行结账即可。
>如果在程序中，需要把2个数据，或者多个数据进行求和的话，那么就需要把这些数据先存储起来，然后把它们累加起来即可；

在Perl中，存储一个数据，需要一个叫做`变量`的东西，如下示例:
```perl
$num1 = 45;  #$num1是一个变量
$num2 = 54;  #$num2也是一个变量
$result = $num1 + $num2 ;#把$num1和$num2这两个"菜篮子"中的数据进行累加，然后放到 result变量中
print $result;
```
上面是数字，字符串也是可以作为变量的，下面是把两条序列给连在一起。
```Perl
$str1 = 'ATCGT';  #$str1是一个变量
$str2 = 'CTGAUTA'; #$str2也是一个变量
$result = $str1 . $str2 ;#把$str1和$str1这两个"菜篮子"中的数据进行合并，然后放到 result变量中
#  $result = $str1$str2 ;
print $result;
# print $str1,$str2 ;
# print "$str1$str2";
# print '$str1$str2'; # 输出是$str1$str2
# print "$str1$str2","\n";
# print "$str1$str2","\n"; # 换行符\n
```
_上面演示单引号和双引号的区别，单引号是输出内容本身而不是存储的数据。_

##### 变量的类型
程序中：为了更充分的利用内存空间以及更有效率的管理内存，变量是有不同的类型的。 
**<标量 `$scalar`>:**
标量 ( scalar ) 是Perl语言中最简单的一种数据结构。这种数据结构的变量可以是数字，字符串，浮点数。在使用时在变量的名字前加上一个美元符号"$"，也称为魔符 ( sigil ) ，表示是标量。
```perl
$a=123;            #数字123
$b="Hello";        #字符串"Hello"
$c=3.14;            #浮点数3.14
```
**<数组 `@array`>：**
  列表 ( list ) 指的是标量的有序集合，而数组 ( array ) 则是储存列表的变量。数组变量以字符"@"开头，索引从0开始。
```perl
#! /usr/bin/perl
my @nums = (45,54);
my @name=("Bob","Tom","Fred");
my $result = $nums[0] + $nums[1];
print '$nums[0] is ',"$nums[0]\n";
print '$nums[1] is ',"$nums[1]\n";
print '$result is ',"$result\n";
```
**<哈希 `%hash`>：**
哈希 ( hash ) 是一个无序的 key/value ( 键/值 ) 对集合。可以使用键作为下标获取值。哈希变量以字符"%"开头。
![var_hash](./image/223ce4e2-2470-4e53-8bed-6e03acaed851.png)
```perl
#!/usr/bin/perl
my %geneid2acc; # 声明全局变量
# 构造哈希表
%geneid2acc = ("AAD12597.1" => "1246500",
               "O85067.1" => "1246500",
               "NP_047184.1" => "1246500");
# 给出键输出值
print $geneid2acc{"AAD12597.1"};
```

>**<数组哈希>：**
>数组哈希使用在perl中，如果想要一个键同时对应多个值，那么数组哈希会非常有用。 
>数组哈希，顾名思义，就是把一整个数组作为哈希某一个键的值存储起来，可以是一维数组，也可以是多维数组。用图说明可能会更容易理解：
>![2d hash](./image/9ae83c8b-7d3b-4485-a940-fe535f641bb0.png)
>`$hash{$key}[$i]`
>![hash](./image/ecb8c701-e8ee-4af9-9455-52fc95341b7f.png)
>`$hash{$key}[$i][1]`表示数组中的第$i行的第2个元素
>**<嵌套哈希（二维哈希）>：**
>![2d hash](./image/f2da39d1-b0bd-4c80-81d2-a4a0b4529960.jpg)

##### 特殊变量
`$1`、`$2`、`$3` # 正则匹配中捕获的变量，后面正则介绍
`$_ ` 默认变量
`$0`  程序名称 
`@ARGV` 命令行参数
`$.`  当前句柄当前行号
除了上面的7个特殊变量，还有很多，详见[Perl菜鸟教程：除了上面的7个特殊变量，还有很多，详见[Perl菜鸟教程:http://www.runoob.com/perl/perl-special-variables.html](http://www.runoob.com/perl/perl-special-variables.html)。

#### 2.5. 函数
##### 标量类函数
**`length()`   # 返回字符串的长度**
```
#!/usr/bin/perl
$DNA = "ATCGAATCGGACTGACGTACGT";
$len_DNA = length($DNA);
print $len_DNA,"\n";
```

**`reverse()` # 将一个字符串反向输出，也可以反转list**
```
#!/usr/bin/perl
$DNA = "ATCGAATCGGACTGACGTACGT";
$DNA_reverse = reverse($DNA); 
print $DNA,"\n";
print $DNA_reverse,"\n";
```

**`chomp()`  # 去掉字符串末尾的换行符**
```
#!/usr/bin/perl
$DNA = "ATCGAATCGGACTGACGTACGT";
# $DNA = "ATCGAATCGGACTGACGTACGT"."\n";
# chomp($DNA);
print $DNA;
```

**`substr()`   # 从字符串中截取一段长度的字符串并将其返回**
  substr EXPR,OFFSET,LENGTH,REPLACEMENT
  substr EXPR,OFFSET,LENGTH
  substr EXPR,OFFSET
```perl
#!/usr/bin/perl
$DNA = "ATCGAATCGGACTGACGTACGT";
# 从第二个字符后，截取长度为10的字符串并返回
$left_str = substr($DNA,2,10);
# 从倒数第5个字符后，截取长度为5的字符串并返回
$right_str = substr($DNA,-5,5);
print '$DNA              -> ',$DNA,"\n";
print 'substr($DNA,2,10) ->  ',$left_str,"\n";
print 'substr($DNA,-5,5) ->                  ',$right_str,"\n";
# 暂时没有用到过
$replace_str = substr($DNA,2,3,"###");
print '$DNA                  -> ',$DNA,"\n";
print 'substr($DNA,2,3,"###") ->  ',$replace_str,"\n";
```

**`split()`     # 按指定分割符分割字符串并把结果放入数组中**
  split /PATTERN/,EXPR,LIMIT
  split /PATTERN/,EXPR
  split /PATTERN/
```perl
#!/usr/bin/perl
# $acc_geneid = "1246500 AAD12597.1";
$acc_geneid = "1246500-AAD12597.1";
print $acc_geneid,"\n";
# @ids = split /\s/,$acc_geneid; # \s代表空格
# @ids = split / /,$acc_geneid; # 也可以直接在//里面打个空格
@ids = split /-/,$acc_geneid; # 也可以直接在//里面打个空格
print '$ids[0] is ',$ids[0],"\n";
print '$ids[1] is ',$ids[1],"\n";
```

**`printf()` 和`sprintf`   # 格式化输出**
  printf FILEHANDLE FORMAT, LIST
  printf FILEHANDLE
  printf FORMAT, LIST
  printf
  sprintf FORMAT, LIST
```perl
#!/usr/bin/perl
 $num = 0.9848;
# $num = 0.9898;
# 输出两位小数
$result = sprintf("%.2f",$num);
# print $result,"\n";
printf "%.2f",$num;
```
另外还有字符串的格式化输出，具体细节自己查阅资料。
**`uc($str)`  转成大写**
**`lc($tr)`      转成小写**

##### 数组类函数
**`scalar()` 数组的元素个数**
```perl
#! /usr/bin/perl
@names=("Bob","Tom","Fred");
$names_len1 = @names;
$names_len2 = scalar(@names);
print $names_len1,"\n";
print $names_len2,"\n";
```

**`join()` 连接LIST **
  join EXPR,LIST
```perl
#! /usr/bin/perl
@names=("Bob","Tom","Fred");
$join_names = join(">",@names);
print $join_names,"\n";
# 输出结果：Bob>Tom>Fred
```

**`push()`  将LIST的元素添加到ARRAY的末尾**
  push ARRAY,LIST
```perl
#! /usr/bin/perl
@names=("Bob","Tom","Fred");
$add_name = "jack";
@add_names = ("jimmy","ken","rose");
push @names,$add_name;
push @names,@add_names;
$join_names = join(">",@names);
print $join_names,"\n";
```
**`sort()`**
  sort SUBNAME LIST
  sort BLOCK LIST
  sort LIST
```perl
#! /usr/bin/perl
@gene_names = ("Nkap","Zc3h3","Crabp1","Atg3","Kbtbd11","Mrpl55","Fmc1","Irf3","Fam234b","Pygb","Rhbdf1");
@nums = (1,34,56,3,12,45,2,5,0);
$sort_naems = join(">", sort @gene_names);
$sort_names_cmp_ab =  join(">", sort {$a cmp $b} @gene_names);
# 上面的两句是一样的,下面的$a,$b换一下位置，顺利就反了一下
$sort_names_cmp_ba =  join(">", sort {$b cmp $a} @gene_names);
$sort_nums_ab = join(">",sort {$a <=> $b} @nums); # 从小到大
$sort_nums_ba = join(">",sort {$b <=> $a} @nums); # 从大到小

print 'sort @gene_names:            ',$sort_naems,"\n";
print 'sort {$a cmp $b} @gene_names:',$sort_names_cmp_ab,"\n";
print 'sort {$b cmp $a} @gene_names:',$sort_names_cmp_ba,"\n";
print 'sort {$a <=> $b} @nums:      ',$sort_nums_ab,"\n";
print 'sort {$b <=> $a} @nums:      ',$sort_nums_ba,"\n";
```
> 进阶函数：
> **`grep()` `map()`**
>   grep BLOCK LIST
>   grep EXPR,LIST
>   map BLOCK LIST
>   map EXPR,LIST
>   通常，用grep来从数组里选择元素，用map来从数组里转换元素。
>   当然，数组处理也能使用标准的循环语句来完成(foreach, for, while, until, do while, do until, redo)，这两个函数比较高级，以后大家可以自学，现在对数组处理暂时使用下面讲的标准循环语句。

##### 哈希类函数
- `keys` 返回HASH的key列表
- `values` 返回HASH的value列表
- `exists()` 判断是否存在某元素，返回布尔值，例如`exists $hash{key}`

```perl
#!/usr/bin/perl
use strict;
use warnings;
my %geneid2acc; # 声明全局变量
# 构造哈希表
%geneid2acc = ("AAD12597.1" => "1246500",
                    "O85067.1" => "1246500",
              "NP_047184.1" => "1246500");
my @geneid2acc_keys =  keys %geneid2acc; # 常用
my @geneid2acc_values = values %geneid2acc; # 不常用
print join("\t",@geneid2acc_keys),"\n";
print join("\t",@geneid2acc_values),"\n";
my $is_exist_1 = exists($geneid2acc{"O85067.1"});
my $is_exist_2 = exists($geneid2acc{"11111"});
print 'exists($geneid2acc{"O85067.1"}):',$is_exist_1,"\n";
print 'exists($geneid2acc{"11111"}):',$is_exist_2,"\n";
```
##### 子函数（用户自定义函数）
划为自学内容，不着急掌握；

#### 2.6. 标示符
**<标示符>：**
什么是标示符，看下图:
![标示符](./image/icon.jpg)
开发人员在程序中自定义的一些符号和名称，标示符是自己定义的,如变量名 、函数名等。
命名规则：**标示符由字母、下划线和数字组成，且数字不能开头**
见名知意：起一个有意义的名字，尽量做到看一眼就知道是什么意思(提高代码可读性) 比如: 名字就定义为name , 定义学生用student，千万不要随便写个a,b,c。
>驼峰命名法：
>![驼峰法](./image/name.jpg)
>小驼峰式命名法（lower camel case）： 第一个单词以小写字母开始；第二个单词的首字母大写，例如：myName、aDog；
>大驼峰式命名法（upper camel case）： 每一个单字的首字母都采用大写字母，例如：FirstName、LastName

**<小总结>：**
不过在程序员中还有一种命名法比较流行，就是用下划线“_”来连接所有的单词，比如send_buf，我个人也是比较习惯这种命名方式，尽量写得自己能够一下子想起来该变量是什么，具体参照[Perl最佳实践第三章（命名规则）](http://www.cnblogs.com/softwaretesting/archive/2011/11/28/2265960.html)。

#### 2.7. 操作符

##### 算术运算符

表格实例中我们设置变量 $a 为 10， $b 为 20。

| 运算符  | 描述          | 实例                    |
| ---- | ----------- | --------------------- |
| +    | 加法运算        | $a + $b 结果为 30        |
| -    | 减法运算        | $a - $b 结果为 -10       |
| *    | 乘法运算        | $a * $b 结果为 200       |
| /    | 除法运算        | $b / $a 结果为 2         |
| %    | 求余运算，整除后的余数 | $b % $a 结果为 0         |
| **   | 乘幂          | $a**$b 结果为 10 的 20 次方 |

##### 比较运算符

表格实例中我们设置变量 $a 为 10， $b 为 20。

| 运算符  | 描述                                       | 实例                   |
| ---- | ---------------------------------------- | -------------------- |
| ==   | 检查两个操作数的值是否相等，如果相等则条件为 true，否则为 false。   | ($a == $b) 为 false   |
| !=   | 检查两个操作数的值是否相等，如果不相等则条件为 true，否则为 false。  | ($a != $b) 为 true。   |
| <=>  | 检查两个操作数的值是否相等, 如果左边的数小于右边的数返回 -1，如果相等返回 0, 如果左边的数大于右边的数返回 1 。 | ($a <=> $b) 返回 -1。   |
| >    | 检查左操作数的值是否大于右操作数的值，如果是则条件为 true，否则为 false。 | ($a > $b) 返回 false。  |
| <    | 检查左操作数的值是否小于右操作数的值，如果是则条件为 true，否则返回 false。 | ($a < $b) 返回 true。   |
| >=   | 检查左操作数的值是否大于或等于右操作数的值，如果是则条件为 true，否则返回 false。 | ($a >= $b) 返回 false。 |
| <=   | 检查左操作数的值是否小于或等于右操作数的值，如果是则条件为 true，否则返回 false。。 | ($a <= $b) 返回 true。  |

以下表格实例中设置变量 $a 为 "abc" ， $b 为 "xyz" ，然后使用比较运算符来计算结果。


| 运算符  | 描述                                       | 实例                   |
| ---- | ---------------------------------------- | -------------------- |
| lt   | 检查左边的字符串是否小于右边的字符串，如果是返回 true，否则返回 false。 | ($a lt $b) 返回 true。  |
| gt   | 检查左边的字符串是否大于右边的字符串，如果是返回 true，否则返回 false。 | ($a gt $b) 返回 false。 |
| le   | 检查左边的字符串是否小于或等于右边的字符串，如果是返回 true，否则返回 false。 | ($a le $b) 返回 true   |
| ge   | 检查左边的字符串是否大于或等于右边的字符串，如果是返回 true，否则返回 false。 | ($a ge $b) 返回 false。 |
| eq   | 检查左边的字符串是否等于右边的字符串，如果是返回 true，否则返回 false。 | ($a eq $b) 返回 false。 |
| ne   | 检查左边的字符串是否不等于右边的字符串，如果是返回 true，否则返回 false。 | ($a ne $b) 返回 true   |
| cmp  | 如果左边的字符串大于右边的字符串返回 1，如果相等返回 0，如果左边的字符串小于右边的字符串返回 -1。 | ($a cmp $b) 返回 -1。   |



##### 赋值运算符

表格实例中我们设置变量 $a 为 10， $b 为 20。

| 运算符  | 描述                               | 实例                              |
| ---- | -------------------------------- | ------------------------------- |
| =    | 简单的赋值运算符，把右边操作数的值赋给左边操作数         | $c = $a + $b 将把 $a + $b 的值赋给 $c |
| +=   | 加且赋值运算符，把右边操作数加上左边操作数的结果赋值给左边操作数 | $c += $a 相等于 $c = $c + $a       |
| -=   | 减且赋值运算符，把左边操作数减去右边操作数的结果赋值给左边操作数 | $c -= $a 相等于 $c = $c - $a       |
| *=   | 乘且赋值运算符，把右边操作数乘以左边操作数的结果赋值给左边操作数 | $c *= $a 相等于 $c = $c * $a       |
| /=   | 除且赋值运算符，把左边操作数除以右边操作数的结果赋值给左边操作数 | $c /= $a 相等于 $c = $c / $a       |
| %=   | 求模且赋值运算符，求两个操作数的模赋值给左边操作数        | $c %= $a 相等于 $c = $c % a        |
| **=  | 乘幂且赋值运算符，求两个操作数的乘幂赋值给左边操作数       | $c **= $a 相等于 $c = $c ** $a     |

##### 位运算

位运算符作用于位，并逐位执行操作。设置 $a = 60，$b = 13

位运算符如下表所示：

| 运算符  | 描述                                       | 实例                                       |
| ---- | ---------------------------------------- | ---------------------------------------- |
| &    | 如果同时存在于两个操作数中，二进制 AND 运算符复制一位到结果中。       | ($a & $b) 将得到 12，二进制为 0000 1100          |
| ^    | 如果存在于其中一个操作数中但不同时存在于两个操作数中，二进制异或运算符复制一位到结果中。 | ($a ^ $b) 将得到 49，二进制为 0011 0001          |
| ~    | 二进制补码运算符是一元运算符，具有"翻转"位效果，即0变成1，1变成0。     | (~$a ) 将得到 -61 ，二进制为 1100 0011 ，一个有符号二进制数的补码形式。 |
| <<   | 二进制左移运算符。左操作数的值向左移动右操作数指定的位数。            | $a << 2 将得到 240 ，二进制为 1111 0000          |
| >>   | 二进制右移运算符。左操作数的值向右移动右操作数指定的位数。            | $a >> 2 将得到 15 ，二进制为 0000 1111           |

##### 逻辑运算符

Perl 逻辑运算符如下表所示。

表格实例中我们设置变量 $a 为 true, $b 为 false。

| 运算符  | 描述                                       | 实例                     |
| ---- | ---------------------------------------- | ---------------------- |
| and  | 逻辑与运算符符。如果两个操作数都为 true，则条件为 true。        | ($a and $b) 为 false。   |
| &&   | C 风格的逻辑与运算符符。如果两个操作数都为 true，则条件为 true    | ($a && $b) 为 false。    |
| or   | 逻辑或运算符。如果两个操作数中有任意一个非零，则条件为 true。        | ($a or $b) 为 true。     |
| not  | 逻辑非运算符。用来反转操作数的逻辑状态。如果条件为 true，则逻辑非运算符将使其为 false。 | not($a and $b) 为 true。 |

##### 引号运算

Perl 引号运算符如下表所示。

| 运算符   | 描述        | 实例                  |
| ----- | --------- | ------------------- |
| q{ }  | 为字符串添加单引号 | q{abcd} 结果为 'abcd'  |
| qq{ } | 为字符串添加双引号 | qq{abcd} 结果为 "abcd" |
| qx{ } | 为字符串添加反引号 | qx{abcd} 结果为 `abcd` |

##### 其他运算符

除了以上我们提到的运算符外，Perl 还支持以下运算符：


| 运算符  | 描述                | 实例                                       |
| ---- | ----------------- | ---------------------------------------- |
| .    | 点号 (.) 用于连接两个字符串。 | 如果 $a="run", $b="oob" ， $a.$b 结果为 "runoob" |
| x    | x 运算符返回字符串重复的次数。  | ('-' x 3) 输出为 ---。                       |
| ..   | .. 为范围运算符。        | (2..5) 输出结果为 (2, 3, 4, 5)                |
| ++   | 自增运算符，整数值增加 1     | $a =10, $a++ will 输出为 11                 |
| --   | 自减运算符，整数值减少 1     | $a =10, $a-- 输出为 9                       |
| ->   | 箭号用于指定一个类的方法      | $obj->$a 表示对象 $obj 的 $a 方法。              |



```perl
#!/usr/bin/perl
$i = 0;
$d = 100;
# ++自增，--自减
print $i++,"\n";
# print $i = $i +1,"\n";
print $d--,"\n";
# print $d = $d -1,"\n";
```

### 3. 判断语句和循环语句

#### 3.1. 判断语句

| 语句                                       | 描述                                       |
| ---------------------------------------- | ---------------------------------------- |
| [if 语句](http://www.runoob.com/perl/perl-if-statement.html) | 一个 **if 语句** 由一个布尔表达式后跟一个或多个语句组成。        |
| [if...else 语句](http://www.runoob.com/perl/perl-if-else-statement.html) | 一个 **if 语句** 后可跟一个可选的 **else 语句**，else 语句在布尔表达式为假时执行。 |
| [if...elsif...else 语句](http://www.runoob.com/perl/perl-if-elsif-statement.html) | 您可以在一个 **if** 语句后可跟一个可选的 **elsif 语句**，然后再跟另一个 **else 语句**。 |
| [unless 语句](http://www.runoob.com/perl/perl-unless-statement.html) | 一个 **unless 语句** 由一个布尔表达式后跟一个或多个语句组成。    |
| [unless...else 语句。](http://www.runoob.com/perl/perl-unless-else-statement.html) | 一个 **unless 语句** 后可跟一个可选的 **else 语句**。   |
| [unless...elsif..else statement](http://www.runoob.com/perl/perl-unless-elsif-statement.html) | 一个 **unless 语句** 后可跟一个可选的 **elsif 语句**，然后再跟另一个 **else 语句**。 |
| [switch 语句](http://www.runoob.com/perl/perl-switch-statement.html) | 在最新版本的 Perl 中，我们可以使用 **switch** 语句。它根据不同的值执行对应的代码块。 |

##### if..else/elsif

```
#!/usr/bin/perl
$name = "Atg3";
$reads_count = 50;    # map到的reads条数

# 1:分两类
# if ($reads_count >100){
# 当条件为真时：
# $status = "high";
# }else{
# 当条件为假时：
# $status = "not high";
# }

# 2:分多个类，多个条件
# if ($reads_count < 20){
    # $status = "very low";
# }elsif($reads_count < 100){
    # $status = "normal";
# }else{
    # $status = "high";
# }

# 3：三元运算符 ? :
# $status = ($reads_count > 100 )? "high" : "not high";

# print "$name - $status\n";

# 4：有时候不需要else，又要if就够了,不需要进行分类：
# if($reads_count >100){
#    print "$name - $reads_count";
# }

# 5：同时满足多个条件，才执行
if(($reads_count < 100) && ($reads_count >20)){
  print "$name - $reads_count";
}
```
##### unless

unless与if相反，当条件判断为假时，才执行。

```
#!/usr/bin/perl
$name = "Atg3";
$reads_count = 50;    # map到的reads条数

# 1:if
if ($reads_count < 100){
# 当条件为真时：
print "if ($reads_count < 100): $name - $reads_count","\n";
}

# 2:unless
unless($reads_count < 100){
print "unless($reads_count < 100): $name - $reads_count","\n";
}
```
#### 3.2. 循环语句

| 循环类型                                     | 描述                                       |
| ---------------------------------------- | ---------------------------------------- |
| [while 循环](http://www.runoob.com/perl/perl-while-loop.html) | 当给定条件为 true 时，重复执行语句或语句组。循环主体执行之前会先测试条件。 |
| [until 循环](http://www.runoob.com/perl/perl-until-loop.html) | 重复执行语句或语句组，直到给定的条件为 true。 循环主体执行之前会先测试条件。 |
| [for 循环](http://www.runoob.com/perl/perl-for-loop.html) | 多次执行一个语句序列，简化管理循环变量的代码。                  |
| [foreach 循环](http://www.runoob.com/perl/perl-foreach-loop.html) | foreach 循环用于迭代一个列表或集合变量的值。               |
| [do...while 循环](http://www.runoob.com/perl/perl-do-while-loop.html) | 除了它是在循环主体结尾测试条件外，其他与 while 语句类似。         |
| [嵌套循环](http://www.runoob.com/perl/perl-nested-loops.html) | 您可以在 while、for 或 do..while 循环内使用一个或多个循环。 |

##### while循环

```
#!/usr/bin/perl
$a = 10;
# 执行 while 循环
while( $a < 20 ){
  print "a 的值为 : $a\n";
  $a = $a + 1;
}
```

一般在生信中，使用while进行逐行读取文件，再循环内对每行进行操作，下面讲到处理外部文件时，举实例。

##### for循环/foreach循环

**<for循环>：**

```perl
#!/usr/bin/perl
# 执行 for 循环用于多次执行一个语句序列
# 这里的$a = $a + 1可以写成$a++;
for( $a = 0; $a <= 10; $a = $a + 1 ){
# for( $a = 0; $a <= 10; $a++ ){
    print "a 的值为: $a\n";
}

# 一般如果是间隔为1，直接这样写：
# $a = 10;
# 1..$a是个数组，也可以写出foreach
# for (1..$a){
# print $_,"\n";
# }
```

**< foreach循环>：**用于迭代一个列表或集合变量的值

```
#!/usr/bin/perl
@list = (2, 12, 36, 42, 51);
# 执行foreach 循环
foreach $a (@list){
    print "a 的值为: $a\n";
}
```

##### 循环控制语句

循环控制语句改变了代码的执行顺序，通过它你可以实现代码的跳转。

| 控制语句                                     | 描述                                       |
| ---------------------------------------- | ---------------------------------------- |
| [next 语句](http://www.runoob.com/perl/perl-next-statement.html) | 停止执行从next语句的下一语句开始到循环体结束标识符之间的语句，转去执行continue语句块，然后再返回到循环体的起始处开始执行下一次循环。 |
| [last 语句](http://www.runoob.com/perl/perl-last-statement.html) | 退出循环语句块，从而结束循环                           |
| [continue 语句](http://www.runoob.com/perl/perl-continue-statement.html) | continue 语句块通常在条件语句再次判断前执行。              |
| [redo 语句](http://www.runoob.com/perl/perl-redo-statement.html) | redo 语句直接转到循环体的第一行开始重复执行本次循环，redo语句之后的语句不再执行，continue语句块也不再执行； |
| [goto 语句](http://www.runoob.com/perl/perl-goto-statement.html) | Perl 有三种 goto 形式：got LABLE，goto EXPR，和 goto &NAME。 |


常用的next语句和last语句，结合判断语句使用：

**< next>：**
```
#!/usr/bin/perl
$a = 10;
while( $a < 20 ){
  if( $a == 15){
      # 跳出迭代
      $a = $a + 1;
      next;
  }
  print "a 的值为: $a\n";
  $a = $a + 1;
}
```
**< last语句>：**
```
#!/usr/bin/perl

$a = 10;
while( $a < 20 ){
  if( $a == 15){
      # 退出循环
      $a = $a + 1;
      last;
  }
  print "a 的值为: $a\n";
  $a = $a + 1;
}
```

##### 嵌套循环（自学）

主要就是一个循环内使用另一个循环。


### 4. 文件、目录操作

#### 4.1. 文件的打开与关闭

**<想一想>：**
>如果想用word编写一份简历，应该有哪些流程呢？
>打开word软件，新建一个word文件
>写入个人简历信息
>保存文件
>关闭word软件
> 同样，在操作文件的整体过程与使用word编写一份简历的过程是很相似的
>打开文件，或者新建立一个文件
>读/写数据
>关闭文件

**< 打开文件>：**
Perl 中打开文件可以使用以下方式：
> open FILEHANDLE, EXPR
> open FILEHANDLE
> sysopen FILEHANDLE, FILENAME, MODE, PERMS
> sysopen FILEHANDLE, FILENAME, MODE
> 参数说明:
> FILEHANDLE：文件句柄，用于存放一个文件唯一标识符。
> EXPR：文件名及文件访问类型组成的表达式。
> MODE：文件访问类型。
> PERMS：访问权限位(permission bits)。
```
open INPUT1,"$query_cover";
open INPUT2,"$acc2geneid_db";
open OUTPUT,">$acc2geneid";
```
#### 4.2. 文件的读写
向文件读写信息有以下几种不同的方式：
<FILEHANDL> 操作符
从打开的文件句柄读取信息的主要方法是 <FILEHANDLE> 操作符。在标量上下文中，它从文件句柄返回单一行。

#### 4.3. 文件的复制、移动、重命名（自学）
练习[perl的文件操作--复制，移动，重命名](http://blog.csdn.net/xuexiaokkk/article/details/51726835)，例如下面的简单例子：
```
# usr/bin/perl
use File::Copy ;
use strict ;
use warnings ;
my @Bin;
@Bin = ("Bin.10","Bin.21","Bin.23","Bin.24_1","Bin.25","Bin.26","Bin.28","Bin.31","Bin.34","Bin.35","Bin.40","Bin.41","Bin.42","Bin.46","Bin.48","Bin.54","Bin.59","Bin.60","Bin.81","Bin.89","Bin.90","Bin.95");

foreach $_(@Bin){
# print("/home/shenmy/Ten_lake_new/3_Contig/LGH/METABAT-SUMMARY_PHYLUM_refine_1/bin_by_bin/$_/$_-contigs.fa","\n");
copy("/home/shenmy/Ten_lake_new/3_Contig/LGH/METABAT-SUMMARY_PHYLUM_refine_1/bin_by_bin/$_/$_-contigs.fa","/home/shenmy/Ten_lake_new/3_Contig/LGH/METABAT-SUMMARY_PHYLUM_refine_1/LGH_refine_gold_bin")||warn "could not copy files :$!" ;
}
# 要求使用绝对路径
```

#### 3.5. 目录（自学）
要求读懂`fastqc_table.pl`代码中操作目录的代码并进行练习：
对fastqc质控得到的*_R1_fastqc.zip和*_R2_fastqc.zip文件批量解压`for i in *.zip;  do unzip $i; done `后，`perl fastqc_table.pl`统计计算“总reads数、GC含量、Q20、Q30”。


| file_name                                | total_reads | GC   | Q20               | Q30               |
| :--------------------------------------- | :---------- | :--- | :---------------- | :---------------- |
| Homo_sapiens_AKAP95_KD_miR_12_293_cell_1_fastqc | 25914821    | 50   | 0.999810031487387 | 0.972201583024633 |
| Homo_sapiens_AKAP95_KD_miR_12_293_cell_2_fastqc | 25914821    | 50   | 0.977900484051192 | 0.934933990090072 |
| Homo_sapiens_AKAP95_KD_miR_8_293_cell_1_fastqc | 29720636    | 50   | 0.992602101445318 | 0.966831930304341 |
| Homo_sapiens_AKAP95_KD_miR_8_293_cell_2_fastqc | 29720636    | 50   | 0.978714654693123 | 0.93782410982053  |
| Homo_sapiens_Control_293_cell_1_fastqc   | 28856780    | 50   | 0.989872946867992 | 0.966726176148248 |
| Homo_sapiens_Control_293_cell_2_fastqc   | 28856780    | 50   | 0.977797280223227 | 0.940547004897982 |


以下代码由Jimmy提供：
```
#! usr/bin/perl 
open OTPUT,">fastqc_table.txt";
opendir (DIR,"./" ) or die "can't open the directory!";
print OTPUT "file_name\ttotal_reads\tGC\tQ20\tQ30\n";
@dir = readdir DIR;
foreach $file ( sort @dir)
{
next unless -d $file;
next if $file eq '.';
next if $file eq '..';
#$file_name=()[];
#print "$file\n";
$total_reads = `grep '^Total' ./$file/fastqc_data.txt`;
# $print $total_reads;
$total_reads=(split(/\s+/,$total_reads))[2];
$GC=`grep  '%GC' ./$file/fastqc_data.txt`;
$GC=(split(/\s+/,$GC))[1];
chomp $GC;

open FH ,"<./$file/fastqc_data.txt";

while (<FH>){
        next unless /#Quality/;
        while (<FH>)
                {
                @F=split ;
                $hash{$F[0]}=$F[1];
                last if />>END_MODULE/;
                }
        }

$all=0;$Q20=0;$Q30=0;
$all+=$hash{$_} foreach keys %hash;
$Q20+=$hash{$_} foreach 0..20;
$Q30+=$hash{$_} foreach 0..30;
$Q20=1-$Q20/$all;
$Q30=1-$Q30/$all;

print OTPUT "$file\t$total_reads\t$GC\t$Q20\t$Q30\n";
#print "$all\n";
}
```

### 5. 正则表达式
正则表达式(regular expression)描述了一种字符串匹配的模式，可以用来检查一个串是否含有某种子串、将匹配的子串做替换或者从某个串中取出符合某个条件的子串等。
Perl语言的正则表达式功能非常强大，基本上是常用语言中最强大的，很多语言设计正则式支持的时候都参考Perl的正则表达式。
Perl的正则表达式的三种形式，分别是匹配，替换和转化:
匹配：m//（还可以简写为//，略去m）
替换：s///
![substitute](./image/substitute.png)
转化：tr///
![tr](./image/tr.png)
这三种形式一般都和 =~ 或 !~ 搭配使用， =~ 表示相匹配，!~ 表示不匹配。


详见[Perl 正则表达式-runoob.com](http://www.runoob.com/perl/perl-regular-expressions.html)

### 6. 单行命令
在熟悉上面的基础语法后，再学习Perl help里面的几个参数后，就可以正式开始学习单行命令。[perl one-lines](http://www.biotrainee.com/thread-313-1-1.html)
![perl oneline](./image/0.2712930559474278.png)

### 7. 包、模块安装和使用
cpanm
[The Comprehensive Perl Archive Network (CPAN) ](https://www.cpan.org/)
[perl模块安装大全](http://mp.weixin.qq.com/s/TJdAs9MmZmLDnTv-k_Lk7Q)
[CIRCOS：PERL AND MODULES](http://circos.ca/documentation/tutorials/configuration/perl_and_modules/)


### 8. 实战练习题
#### 8.1. 准备测试数据
```
安装bowtie2这个软件，里面有测试数据！
## Download and install bowtie
cd ~/biosoft
mkdir bowtie &&  cd bowtie
wget https://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.2.9/bowtie2-2.2.9-linux-x86_64.zip  
unzip bowtie2-2.2.9-linux-x86_64.zip
```
#### 8.2. 了解fastq格式
详见：[https://wiki2.org/en/FASTQ_format](https://wiki2.org/en/FASTQ_format)
http://zjuwhw.github.io/2016/08/13/fastq_format.html
Phred quality score是用来测定DNA自动测序中每个核算的测序质量的。它最早是为了人类基因组计划中的程序Phred base calling而开发的。
公式：Q = -10 * log10(P) <==> P = 10 ^(-Q/10)
这里Q为Phred quality score，P为base-calling的error probabilities（错误率）。

对于原始的序列，质量值的范围取决于所用的技术和base caller，但是对于目前的Illumina技术，最多能达到41。
另外，常用软件FastQC和Trimmomatic均可以自动判断encoding的。
http://www.bioinformatics.babraham.ac.uk/projects/fastqc/
因此，质量值BBBBCCCC?<A?BC?7@@???????DBBA@@@@A@@：

若采用Phred+64编码，应该为

```
2,2,2,6,6,6,6,6,6,6,6,6,6,9,9,9,9,9,9,9,9,9,9,9,9,9,9,2,6,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,9,6,6,6,6,6,6,6,6,6,6,2,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,6,2,6,6,6,6,6,6,6
```

若采用Phred+32编码，应该为

```
33,33,33,37,37,37,37,37,37,37,37,37,37,40,40,40,40,40,40,40,40,40,40,40,40,40,40,33,37,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,40,37,37,37,37,37,37,37,37,37,37,33,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,37,33,37,37,37,37,37,37,37
```


故，很明显，使用的是“Phred+32”编码，且质量值均大于30，错误率均小于千分之一，准确率大于99.9%。



![phred](./image/0.850128971211318.png)

#### 8.3. 题目要求
##### 对FASTQ的操作
- 5,3段截掉几个碱基 ：`substr()`  `length()`
- 序列长度分布统计：`length()`  `mean()`  ` max()`  `min()` 
- FASTQ 转换成 FASTA ：输出header行和序列行（第一和第四行）
- 统计碱基个数及GC%  ：`tr///`
##### 对FASTA的操作
- 取互补序列 `tr/ATCGatcg/TAGCtagc/` 
- 取反向序列 `reverse()` 
- DNA to RNA `s/T/U/g; `
- 大小写字母形式输出 `tr/atcg/ATCG/`  `tr/ATCG/atcg/` 
- 每行指定长度输出序列 `length()`  `for`  `substr()` 
- 按照序列长度/名字排序 `length()`  哈希 `sort{$a<=>$b}` 
- 提取指定ID的序列 
- 随机抽取序列
- 去格式化
- 根据id列表获取序列
- 打乱fasta文件顺序
- 过滤小于500的序列