# 构建基本shell脚本

内容

- 使用多个命令
- 创建脚本文件
- 显示消息
- 使用变量
- 输入输出重定向
- 管道
- 数学运算
- 退出脚本

## 一个脚本例子

```
bed=exon_probe.hg38.gene.bed
for bam in  /home/project/*.bam
do
file=$(basename $bam )
sample=${file%%.*}
echo $sample
export total_reads=$(samtools idxstats  $bam|awk -F '\t' '{s+=$3}END{print s}')
echo The number of reads is $total_reads
bedtools multicov -bams  $bam -bed $bed |perl -alne '{$len=$F[2]-$F[1];if($len <1 ){print "$.\t$F[4]\t0" }else{$rpkm=(1000000000*$F[4]/($len* $ENV{total_reads}));print "$.\t$F[4]\t$rpkm"}}' >$sample.rpkm.txt
done
```

## 使用多个命令

如果多个命令一起使用，可以放在一行并用分号分隔。

```shell
wsx@wsx-ubuntu:~$ date; who
2017年 07月 26日 星期三 09:53:43 CST
wsx      tty7         2017-07-26 09:48 (:0)
```

## 创建脚本文件

在创建脚本文件时，必须在文件的第一行指定要使用的shell，格式为：

```
#!/bin/bash
```

脚本文件的第一行中`#`后的惊叹号会告诉shell使用哪个shell来运行脚本（如果是其他编码语言脚本，像python，第一行类似）。

其他地方的`#`用作注释行。

添加名为`test1`的脚本文件，内容为：

```
#!/bin/bash
# This script displays the date and who's logged on
date
who
```

## 运行一个脚本怎么会那么难？

现在运行脚本，结果会是：

```
wsx@wsx-ubuntu:~/script_learn$ test1
未找到 'test1' 命令，您要输入的是否是：
 命令 'testr' 来自于包 'python3-testrepository' (main)
 命令 'testr' 来自于包 'python-testrepository' (universe)
 命令 'test' 来自于包 'coreutils' (main)
test1：未找到命令
```

我们现在需要做的是让bash shell能够找到我们的脚本文件。shell会通过`PATH`环境变量来查找命令，我们可以看看：

```shell
wsx@wsx-ubuntu:~/script_learn$ echo $PATH
/home/wsx/Anaconda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

很显然，**我们的文件没有在这些目录范围内**。要让shell找到test1脚本，我们可以采取以下两种做法之一：

- 将shell脚本文件所处的目录添加到`PATH`环境变量中；
- 在提示符中用绝对路径或相对路径来引用shell脚本文件。

第二种方法比较简单，我们在这里试试：

```shell
wsx@wsx-ubuntu:~/script_learn$ ./test1
bash: ./test1: 权限不够
wsx@wsx-ubuntu:~/script_learn$ ll test1 # 发现权限不够，查看文件的权限
-rw-rw-r-- 1 wsx wsx 73 7月  26 10:03 test1
wsx@wsx-ubuntu:~/script_learn$ chmod u+x test1 # 修改权限，添加可执行属性
wsx@wsx-ubuntu:~/script_learn$ ./test1 # 成功运行脚本
2017年 07月 26日 星期三 10:09:23 CST
wsx      tty7         2017-07-26 09:48 (:0)

```

## 显示消息

在`echo`命令后面加上一个字符串，就能显示出这个文本字符串。这种方式可以添加自己的文本消息来告诉脚本用户脚本正在做什么。

```shell
wsx@wsx-ubuntu:~/script_learn$ echo This is a test
This is a test
```

如果文本本身带有字符串，我们需要**用单引号或双引号来划定文本字符串**。

```shell
wsx@wsx-ubuntu:~/script_learn$ echo "Let's see if this'll work"
Let's see if this'll work
```

我们修改下之前的test1文件，增加消息显示：

```shell
#!/bin/bash
# This script displays the date and who's logged on
echo The time and date are:
date
echo "Let's see who's logged into the system"
who
```

运行：

```shell
wsx@wsx-ubuntu:~/script_learn$ ./test1
The time and date are:
2017年 07月 26日 星期三 10:17:59 CST
Let's see who's logged into the system
wsx      tty7         2017-07-26 09:48 (:0)
```

**如果想把文本字符串和命令输出显示在同一行中**，可以用`echo`语句的`-n`参数。需要在字符串的两侧加上引号，并且保证字符串尾部有一个空格（不然字符串和命令输出就粘连到一起了）。

```shell
#!/bin/bash
# This script displays the date and who's logged on
echo -n "The time and date are: " 
date
echo  "Let's see who's logged into the system: " 
who

# 运行结果输出
wsx@wsx-ubuntu:~/script_learn$ ./test1
The time and date are: 2017年 07月 26日 星期三 10:24:04 CST
Let's see who's logged into the system: 
wsx      tty7         2017-07-26 09:48 (:0)

```

## 使用变量

变量允许我们临时性地将信息存储在shell脚本中，以便和脚本中的其他命令一起使用。

**环境变量**

shell维护着一组环境变量，用来记录特定的系统信息。比如系统的名称、登录到系统上的用户名、用户的系统ID(也称为UID)、用户默认主目录以及shell查找程序的搜索路径。

使用`set`命令显示一份完整的当前环境变量列表。`env`与`printenv`命令都可以显示全局变量。（这些命令输出结果比较多，不展示了。之前关于环境变量的笔记有比较详细的描述。）

在环境变量名称之前加上美元符可以使用这些环境变量。

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test2
#! /bin/bash
# display user information from the system
echo "User info for userid: $USER"
echo UID: $UID
echo HOME: $HOME
wsx@wsx-ubuntu:~/script_learn$ chmod u+x test2
wsx@wsx-ubuntu:~/script_learn$ ./test2
User info for userid: wsx
UID: 1000
HOME: /home/wsx
```

可以想象的到，如果我们想要使用实际的美元符而不是引用变量，肯定会出问题。这时候我们需要在美元符前面加上`\`进行转义，以显示美元符本身。

**用户变量**

使用等号将值赋给用户变量。**注意，在变量、等号和值之间不能出现空格！**这个是初学者常见的一个问题，本人也非常不太适应这个。因为在其他语言中不区分等号两边的空格，相信接触过其他脚本的朋友们肯定有习惯打空格使代码美观的，这在bash shell中是万万行不通滴。

一个使用的例子：

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test3
#!/bin/bash
# testing variables
days=10
guest="Katie"
echo "`$guest checked in $`days days ago"
days=5
guest="Jessica"
echo "`$guest checked in $`days days ago"
wsx@wsx-ubuntu:~/script_learn$ chmod u+x test3
wsx@wsx-ubuntu:~/script_learn$ ./test3
Katie checked in 10 days ago
Jessica checked in 5 days ago
```

变量每次被引用时，都会输出当前赋给它的值。重要的是要记住，引用一个变量值时需要使用美元符，而引用变量来对其进行赋值时则不需要使用美元符。

## 命令替换

shell脚本最有用的特性之一就是可以从命令输出中提取信息，并将其赋给变量。

有两种方法可以将命令输出赋给变量：

- 反引号字符(`)
- $()格式

```
要么用一对反引号把整个命令行命令围起来：
testing=`date`
要么使用$()格式
testing=$(date)
```

下面是一个例子，在脚本中通过命令替换获得当前日期并用它来生成唯一文件名：

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test4
#!/bin/bash
# copy the /usr/bin directory listing to a log file
today=$(date +%y%m%d)
ls /usr/bin -al > log.$today

wsx@wsx-ubuntu:~/script_learn$ chmod u+x test4
wsx@wsx-ubuntu:~/script_learn$ ./test4
```

## 重定向输入和输出

通过几个操作符进行重定向，我们可以将命令的结果输出到另外的位置（文件）。当然，重定向可以用于输入。

**输出重定向**

最基本的操作符是`>`。比如我们想要输出命令结果到一个指定文件：

```shell
wsx@wsx-ubuntu:~/script_learn$ date > test6
wsx@wsx-ubuntu:~/script_learn$ ls -l test6
-rw-rw-r-- 1 wsx wsx 43 7月  26 16:42 test6
wsx@wsx-ubuntu:~/script_learn$ cat test6
2017年 07月 26日 星期三 16:42:34 CST
```

如果想要将命令的输出追加到已有文件中，需要用双大于号（>>）来追加数据。

**输入重定向**

输入重定向和输出重定向正好相反。输入重定向将文件的内容重定向到命令，而非将命令的输出重定向到文件。

使用的符号是小于号（<）。

> 一种简单的记忆方法是：在命令行上，命令总是在左侧，而重定向符号“指向”数据流动的方向。小于号说明数据正在从输入文件流向命令。

比如用wc命令检查文本的行数、词数和字节数。

```shell
wsx@wsx-ubuntu:~/script_learn$ wc < test6
 1  6 43
```

另一种输入重定向的方法是**内联输入重定向**。它无需使用文件进行重定向，只需要在命令行中指定用于输入重定向的数据即可。它使用的符号是远小于号（<<），除了这个符号，我们还需要指定一个文本标记用来划分输入数据的开始和结尾。任何字符串都可以作为文本标记，但在数据的开始和结尾文本标记必须一致。

```shell
wsx@wsx-ubuntu:~/script_learn$ wc << EOF
> test string1
> test string2
> test string3
> EOF
 3  6 39
```

它的形式为：

```
command << marker
data
marker
```

## 管道

有时候需要将一个命令的输出作为另一个命令的输入。通过`|`符号分隔命令即可实现管道。

比如我想查看某个文件（test1）的前两行并进行排序，操作如下：

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test1
#!/bin/bash
# This script displays the date and who's logged on
echo -n "The time and date are: " 
date
echo  "Let's see who's logged into the system: " 
who
wsx@wsx-ubuntu:~/script_learn$ cat test1 | head -2 | sort
#!/bin/bash
# This script displays the date and who's logged on
```

管道的强大之处在于可以根据自己的需求灵活地组合和使用各种linux命令工具。这里只是一个简单的例子，要熟练掌握少不了平时多多研究和练习。

## 执行数学运算

对shell脚本来说，执行数学运算非常麻烦。有两种实现方式。

**expr**命令

`expr`命令允许在命令行上处理数学表达式，但是特别笨拙。（Bourne shell中）

```shell
wsx@wsx-ubuntu:~/script_learn$ exrpr 1 + 5
未找到 'exrpr' 命令，您要输入的是否是：
 命令 'expr' 来自于包 'coreutils' (main)
exrpr：未找到命令

```

看到没有，那算了。它基本涉及的操作跟我们使用的其他语言是一致的。但是有些问题需要处理，像`*`是通配符，在运算是是做乘号处理的，需要进行转义。

**使用方括号**

bash shell提供了一种更简单的方法来执行数学表达式。在bash中，在将一个数学运算结果赋给某个变量时，可以用美元符和方括号（$[operator]）将数学表达式围起来。

```shell
wsx@wsx-ubuntu:~/script_learn`$ var1=$`[1+5]
wsx@wsx-ubuntu:~/script_learn`$ echo $`var1
6
wsx@wsx-ubuntu:~/script_learn`$ var2=$`[$var1+2]
wsx@wsx-ubuntu:~/script_learn`$ echo $`var2
8
```

这种方式不仅方便，而且因为在方括号内，不会让shell误解乘号或其他符号。

但bash shell计算有一个主要限制：**它只支持整数运算！**

**浮点解决方案**

最常见的方案是用内建的bash计算器。它实际上是一门编程语言，它允许在命令行中输入浮点表达式，然后解释并计算该表达式，最后返回结果。bash计算器能够识别：

- 数字（整数和浮点数）
- 变量（简单变量和数组）
- 注释（/* */开始的行）
- 表达式
- 编程语句
- 函数

```shell
wsx@wsx-ubuntu:~/script_learn$ bc
bc 1.06.95
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
12 * 5.4
64.8
3.156 * (3 + 5)
25.248
quit
```

在脚本中使用bc

可以用命令替换运行bc命令，并将输出赋给一个变量。基本格式如下：

```
variable=$(echo "options; expression" | bc)
```

options设置变量，expression参数定义了通过bc执行的数学表达式。

看一个简单实例：

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test9
#!/bin/bash
var1=$(echo "scale=4; 3.44/5" | bc)
echo The answer is $var1
wsx@wsx-ubuntu:~/script_learn$ chmod u+x test9
wsx@wsx-ubuntu:~/script_learn$ ./test9
The answer is .6880
```

这个例子将`scale`变量设置为四位小数，并在`expression`部分指定了特定的运算。

这个方法适用于较短的运算，但有时我们会涉及更多的数字。如果需要进行大量运算，在一个命令行中列出多个表达式就会有点麻烦。

这里有一个解决方法：使用内联输入重定向，将一个文件重定向到bc命令来处理。格式为：

```
variable=$(bc << EOF
options
statements
expressions
EOF)
```

`EOF`文本字符串标识了内联重定向数据的起始。注意，仍然需要命令替换符号将bc命令的输出赋给变量。

下面是一个例子：

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test10
#!/bin/bash

var1=10.46
var2=43.67
var3=33.2
var4=71

var5=$(bc <<EOF
scale=4
a1 = ( `$var1 * $`var2)
b1 = ( `$var3 * $`var4)
a1 + b1
EOF
)

echo The final answer for this mess is $var5
wsx@wsx-ubuntu:~/script_learn$ chmod u+x test10
wsx@wsx-ubuntu:~/script_learn$ ./test10
The final answer for this mess is 2813.9882
```

在普通的shell脚本中，数字默认当做字符串处理。这也是为什么我们脚本处理计算麻烦和我们需要特定的工具和方法来进行处理。一定要注意区分。

## 退出脚本

前面运行的脚本都是命令执行完成，脚本自动结束。其实我们可以用更为优雅的方式告诉shell命令运行完成，因为每个命令都使用**退出状态码（exit status）**，它是一个0-255的整数值，我们可以捕获这个值并在脚本中使用。

Linux提供了一个专门的变量`$?`来保存上个已执行命令的退出状态码。

```shell
wsx@wsx-ubuntu:~/script_learn$ date
2017年 07月 27日 星期四 10:44:18 CST
wsx@wsx-ubuntu:~/script_learn`$ echo $`?
0
```

按照惯例，一个成功结束的命令的退出状态码是0。如果有错误，则显示一个正数值。

Linux错误退出状态码没有什么标准，但有一些参考：

| 状态码   | 描述               |
| ----- | ---------------- |
| 0     | 命令成功结束           |
| 1     | 一般性未知错误          |
| 2     | 不适合的shell命令      |
| 126   | 命令不可执行           |
| 127   | 没找到命令            |
| 128   | 无效的退出参数          |
| 128+x | 与Linux信号x相关的严重错误 |
| 130   | 通过Ctrl+C终止的命令    |
| 255   | 正常范围之外的退出状态码     |

```shell
wsx@wsx-ubuntu:~/script_learn$ asfg
未找到 'asfg' 命令，您要输入的是否是：
 命令 'asdfg' 来自于包 'aoeui' (universe)
asfg：未找到命令
wsx@wsx-ubuntu:~/script_learn`$ echo $`?
127
```

**exit命令**

默认，shell脚本会以脚本最后的一个命令的退出状态码退出。

但是我们可以改变这种默认行为，返回自己的退出状态码。exit命令允许在脚本结束时指定一个状态退出码。

```shell
wsx@wsx-ubuntu:~/script_learn$ cat test13
#!/bin/bash
# testing the exit status
var1=10
var2=30
var3=`$[$`var1 + $var2]
echo The answer is $var3
exit 5

wsx@wsx-ubuntu:~/script_learn$ chmod u+x test13
wsx@wsx-ubuntu:~/script_learn$ ./test13
The answer is 40
wsx@wsx-ubuntu:~/script_learn`$ echo $`?
5
```

注意最大255，如果大于它，得到的是求模的结果（余数）。


## 脚本高级知识

还有一些脚本高级知识，不予讲解，感兴趣的同学可以自行购买相关书籍和较小视频自学：
- 处理信号
- 以后台模式运行脚本
- 禁止挂起
- 作业控制
- 修改脚本优先级
- 脚本执行自动化

本文作者：**DivinerSX**