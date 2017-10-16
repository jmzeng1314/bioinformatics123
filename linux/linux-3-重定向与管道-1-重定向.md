---
author: sam
comments: true
date: 2017-07-06 11:36:37+00:00
#layout: post
link: http://hugo.qinqianshan.com/shell-output-redirection-21/
slug: shell-output-redirection-21
title: Linux【3】-重定向与管道1-文件内容查阅输出重定向（> >> 2>&1）
wordpress_id: 947
categories:
- shell script(来个bash呗)
tags:
- 输出重定向
---


Ken Thompson 曾经就如何设计简洁而高效的操作系统提出过一个原则，
就是我们现在奉为圭臬的 Unix 程序设计准则（Unix Philosophy）：“一次只
做一件事，并做到最好（Do one thing and do itwell）”。Linux 作为一种类
Unix 操作系统也秉承了这个准则。如果仔细回想一下，我们会发现我们前面所
提到过的各种 Linux 程序（命令）无不体现了这个准则：这些程序大多目的明
确专一，并且往往能够又快又好地达成目标。

Doug McIlroy 将 Unix 准则扩展并归纳如下，一个好的程序应该：

    a)一次只做一件事，并做到最好；
    b)能够与其他程序协同工作；
    c)能够处理文本流。

(Write programs that doone thing and do it well. Write programs to work
together. Write programs tohandle text streams, because that is a universal
interface)。我们接下来要讨论的就是：如何有效地让 Linux 的程序协同工作。

其中主要涉及到如下两个主题:
 
* 对命令的输入/输出文本流进行重定向(Redirection)；
* 将一个命令的输出通过管道(Pipes)连接到另一个命令的输入。
接下来，我们就对这两个主题进行一个详细阐述。

输入/输出文本流和重定向
所谓输入/输出文本流很容易理解，我们在“初次接触 Linux”的任务 1
中提到过，Linux 通过 Shell 与用户进行交互。而 Shell 正是使用输入流向程序
提供输入，常常指的是终端键盘；而用输出流在终端上显示程序输出，以前往
往指的是 ASCII 打字机或终端显示屏，现在一般是图形桌面上的窗口。
不过确切来说，Bash 实际上使用三种标准输入输出流：

* 标准输出流（stdout），显示来自命令的输出，文件描述符是 1。
* 标准错误输出流（stderr），显示来自命令的错误输出，文件描述符是2。
* 标准输入流（stdin），向命令提供输入，文件描述符是 0。

对于任何一条 linux 命令执行，它会是这样一个过程：
先有一个输入：输入可以从键盘或者其他输入设备，通过 stdin 交给命
令。命令执行完成：成功了，会把成功结果通过 stdout 输出到输出设备上：；
如果命令执行有错误：会把错误结果通过 stderr 也输出到屏幕上面。一般情况
下，我们可以将 stdout 和 stderr 视为终端屏幕或者终端窗口，而将 stdin 视为
终端键盘。



## 一、bash中0，1，2

bash中0，1，2三个数字分别代表STDIN_FILENO、STDOUT_FILENO、STDERR_FILENO，即标准输入（一般是键盘），标准输出（一般是显示屏，准确的说是用户终端控制台），标准错误（出错信息输出）
<!-- more -->


## 二、输入输出重定向


所谓重定向输入就是在命令中指定具体的输入来源，譬如 cat < test.c 将test.c重定向为cat命令的输入源。输出重定向是指定具体的输出目标以替换默认的标准输出，譬如ls > 1.txt将ls的结果从标准输出重定向为1.txt文本。有时候会看到如 ls >> 1.txt这类的写法，> 和 >> 的区别在于：> 用于新建而>>用于追加。即ls > 1.txt会新建一个1.txt文件并且将ls的内容输出到新建的1.txt中，而ls >> 1.txt则用在1.txt已经存在，而我们只是想将ls的内容追加到1.txt文本中的时候。

默认输入只有一个（0，STDIN_FILENO），而默认输出有两个（标准输出1 STDOUT_FILENO，标准错误2 STDERR_FILENO）。因此默认情况下，shell输出的错误信息会被输出到2，而普通输出信息会输出到1。但是某些情况下，我们希望在一个终端下看到所有的信息（包括标准输出信息和错误信息），要怎么办呢？
对了，你可以使用我们上面讲到的输出重定向。思路有了，怎么写呢？ 非常直观的想法就是2>1（将2重定向到1嘛），行不行呢？试一试就知道了。我们进行以下测试步骤：

    
     1）mkdir test && cd test; 创建test文件夹并进入test目录
     2）touch a.txt b.c c; 创建a.txt b.c c 三个文件
     3）ls > 1; 按我们的猜测，这句应该是将ls的结果重定向到标准输出，因此效果和直接ls应该一样。但是实际这句执行后，标准输出中并没有任何信息。
     4）ls; 执行3之后再次ls，则会看到test文件夹中多了一个文件1
     5）cat 1 ; 查看文件1的内容，实际结果为：1 a.txt b.c c


可见步骤3中 ls > 1并不是将ls的结果重定向为标准输出，而是将结果重定向到了一个文件1中。即1在此处不被解释为STDOUT_FILENO，而是文件1。

4、到了此时，你应该也能猜到2>&1的用意了。不错，2>&1就是用来将标准错误2重定向到标准输出1中的。此处1前面的&就是为了让bash将1解释成标准输出而不是文件1。至于最后一个&，则是让bash在后台执行


## 三、例子

### 1. 输出重定向：

1.1把标准输出重定向到文件
    
    [~]# echo "hello" > test.sh
     [~]# cat test.sh
     hello
     '>'输出方式默认等价'1>'
     [~]# echo "hello" 1> test.sh
     [~]# cat test.sh
     hello

但是错误内容还是会显示在屏幕上
    
    [~]$cat edit.sql /root/test.sh > temp.sh
     cat: /root/test.sh: Permission denied

可以把错误内容也输出到文件中（利用文件描述符）：
    
    [~]$cat edit.sql /root/test.sh 1> temp.sh 2> error.sh
     [~]$cat temp.sh
     select dbms_rowid.rowid_object('AAAZdQAAGAAATxjAAk') data_object_id#,
     dbms_rowid.rowid_relative_fno('AAAZdQAAGAAATxjAAk') rfile#,
     dbms_rowid.rowid_block_number('AAAZdQAAGAAATxjAAk') block#,
     dbms_rowid.rowid_row_number('AAAZdQAAGAAATxjAAk') row# from dual
     /
     [~]$cat error.sh
     cat: /root/test.sh: Permission denied


把标准输出和错误信息写入同一个文件：
    
    [~]$cat edit.sql /root/test.sh > temp.sh 2>&1
     [~]$cat temp.sh
     select dbms_rowid.rowid_object('AAAZdQAAGAAATxjAAk') data_object_id#,
     dbms_rowid.rowid_relative_fno('AAAZdQAAGAAATxjAAk') rfile#,
     dbms_rowid.rowid_block_number('AAAZdQAAGAAATxjAAk') block#,
     dbms_rowid.rowid_row_number('AAAZdQAAGAAATxjAAk') row# from dual
     /
     cat: /root/test.sh: Permission denied


### 2.输入重定向：

tr命令往往用于从标准输入中替换特定字符， 并将结果写入到标准输出

    tr [准备要替换的字符] [要替换成的字符]
 
 例子：
    
    tr ' ' '\t' <./fruit.txt  

    sort -k2 <<EOF
    > 1 apple
    > 2 pear
    > 3 banana
    >EOF

    结尾的EOF前面和后面 不要有空格


**参考资料：**

http://www.jb51.net/article/45202.htm

http://blog.csdn.net/feng27156/article/details/38980543

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004
