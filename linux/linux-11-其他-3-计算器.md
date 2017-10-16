
---
author: sam
comments: true
date: 2015-10-09 11:11:03+00:00
#layout: post
link: http://hugo.qinqianshan.com/bc-and-expr-dc-echo-under-the-awk-linux-calculator/
slug: bc-and-expr-dc-echo-under-the-awk-linux-calculator
title: Linux【11】-其他3-Linux下的计算器（bc/expr/dc/echo/awk）
wordpress_id: 742
categories:
- basic_description
tags:
- awk
- bc
- dc
- echo
- expr
---

<blockquote>今天看主管再次炫技，在Linux直接做一些简单的计算，感觉蛮好玩的，回来尝试一下。Linux下的计算器有如下几个bc、expr、dc、echo、awk</blockquote>

<!-- more -->

## 一、bc

bc在默认的情况下是个交互式的指令。在bc工作环境下，可以使用以下计算符号：
    
    + 加法 
    - 减法 
    * 乘法 
    / 除法 
    ^ 指数 
    % 余数

如：
    
终端下输入：bc

    bc 1.06
    Copyright 1991-1994, 1997, 1998, 2000 Free Software Foundation, Inc.
    This is free software with ABSOLUTELY NO WARRANTY.
    For details type `warranty'. 
    3+6 <=加法
    9
    4+2*3 <=加法、乘法
    10
    (4+2)*3 <=加法、乘法（优先）
    18
    4*6/8 <=乘法、除法
    3
    10^3 <=指数
    1000
    18%5 <=余数
    3+4;5*2;5^2;18/4 <=一行输入多个计算，用;相隔。
    7
    10
    25
    4
    quit <=退出
     
    # bc
    bc 1.06
    Copyright 1991-1994, 1997, 1998, 2000 Free Software Foundation, Inc.
    This is free software with ABSOLUTELY NO WARRANTY.
    For details type `warranty'. 
    1/2
    0
    <span style="color: #ff0000;">scale=4 <=设小数位</span>
    1/2
    .5000
    
    quit

以上是交互的计算，那到也可以不进行交互而直接计算出结果。

A.用echo和|法，如：
    
    # echo "(6+3)*2" |bc
    18
    # echo 15/4 |bc
    3
    # echo "scale=2;15/4" |bc
    3.75
    # echo "3+4;5*2;5^2;18/4" |bc
    7
    10
    25
    4

另外，bc除了scale来设定小数位之外，还有ibase和obase来其它进制的运算。
如：
    
    //将16进制的A7输出为10进制, 注意，英文只能大写
    # echo "<span style="color: #ff0000;">ibase=16</span>;A7" |bc
    167
    //将2进制的11111111转成10进制
    # echo "ibase=2;11111111" |bc
    255
    //输入为16进制，输出为2进制
    # echo "<span style="color: #ff0000;">ibase=16;obase=2</span>;B5-A4" |bc
    10001

对于bc还有补充，在bc --help中还可以发现：bc后可以接文件名。如：
    
    # more calc.txt 
    3+2
    4+5
    8*2
    10/4
    # bc calc.txt 
    5
    9
    16
    2


## 二、expr

expr命令可不光能计算加减乘除哦，还有很多表达式，都可以计算出结果，不过有一点需要注意，在计算加减乘除时，不要忘了使用空格和转义。下面直接用实例来介绍一下expr的运算，如：
    
    # expr 6 + 3 （<span style="color: #ff0000;">6和+后有空格</span>）
    9
    
    # expr 2 \* 3 （有转义符号，有空格）
    6
    # expr 14 % 9 
    5 
     
    # a=3
    # expr $a+5 （注意空格）
    3+5
    # expr $a + 5 （变量，有空格）
    8
    # a=`expr 4 + 2`
    echo $a
    6
    # expr $a + 3
    9

另外，expr对于字串的操作（计算）也是很方便的，如：
    
    //字串长度 
    # expr <span style="color: #ff0000;">length</span> "yangzhigang.cublog.cn" 
    21
     
    //从位置处抓取字串 
    # expr <span style="color: #ff0000;">substr</span> "yangzhigang.cublog.cn" 1 11
    yangzhigang
    
    //字串开始处
    # expr <span style="color: #ff0000;">index</span> "yangzhigang.cublog.cn" cu
    13

## 三、dc

用dc来进行计算的人可以不多，因为dc与bc相比要复杂，但是在进行简单的计划时，是差不多的，不算难。dc为压栈操作，默认也是交互的，但也可以用echo和|来配合打算。

选择参数
    
    -e<脚本> 增加脚本中的命令到程序的命令设置
    -f<脚本文件> 增加脚本文件中的命令到程序的命令设置
    --help 显示帮助信息
    --version 显示版本信息

【运算类型】
    
    + ：加
    - ：减
    * ：乘
    / ：除
    % ：余数
    ^ ：指数
    v ：开方

    [root@redhat ~]# dc
    56 //输入乘数
    9 //输入被乘数
    * //乘号
    p //输出结果
    504
    q //退出
    
    [s_tools]$dc -e "4k11 41/p"
     .2682
    
    [root@redhat ~]# # echo 3 2+ 4* p |dc
    20

## 四、echo

echo用来进行回显，是周知的事。上面也配合bc来进行计算。其实echo也可以单独进行简单的计算，如：
    
    # echo $((3+5))
    8
    # echo $(((3+5)*2))
    16
     
    echo还可以进行变量的计算，如：
    # a=10
    # b=5
    # echo $(($a+$b))
    15
    # echo $a+$b
    10+5
    # echo $a+$b |bc
    15
     
    //<strong>计算前天的日期</strong>
    # echo `date +%Y%m%d`
    20090813
    # echo `date +%Y%m%d`-2
    20090813-2
    # echo `date +%Y%m%d`-2 |bc
    20090811

## 五、AWK

awk在处理文件的时，可以进行运算，那当然也可以单单用来计算了，如：
    
    # awk 'BEGIN{a=3+2;print a}'
    5
    # awk 'BEGIN{a=(3+2)*2;print a}'
    10

**参考资料：**

http://blog.chinaunix.net/uid-24673811-id-1760837.html

http://blog.sina.com.cn/s/blog_6104d9ea0101a2np.html
