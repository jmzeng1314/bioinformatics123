---
author: sam
comments: true
date: 2017-07-16 15:25:16+00:00
#layout: post
link: http://hugo.qinqianshan.com/file-contents-check-out-cattacnlmoreless/
slug: file-contents-check-out-cattacnlmoreless
title: Linux【2】-管理文件11-文件内容查阅(cat/tac/nl/more/less/head/tail)
wordpress_id: 1534
categories:
- basic_description
tags:
- cat
- less
- more
- nl
- tac
---

    cat 由第一行开始显示档案内容
    tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
    nl 显示的时候，顺道输出行号！
    more 一页一页的显示档案内容
    less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
    head 只看头几行
    tail 只看尾巴几行
    od 以二进制的方式读取档案内容！
<!-- more -->


## 一、cat

cat 是 Concatenate （连续）的简写， 主要的功能是将一个档案的内容连续的印出在屏幕上面！    

三个功能：

1. 连接几个文件
2. 从键盘创建文件
3. 查看文件（不支持上翻，适用于小文件）

    
[root@www ~]# cat [-AbEnTv]

    选项与参数：
    -A ：相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
    -b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
    -E ：将结尾的断行字符 $ 显示出来；
    -n ：打印出行号，连同空白行也会有行号，与 -b 的选项不同；
    -T ：将 [tab] 按键以 ^I 显示出来；
    -v ：列出一些看不出来的特殊字符
    
范例一：检阅 /etc/issue 这个档案的内容
       
    [root@www ~]# cat /etc/issue
    CentOS release 5.3 (Final)
    Kernel \r on an \m
    
范例二：承上题，如果还要加印行号呢？    
    
    [root@www ~]# cat -n /etc/issue
    1 CentOS release 5.3 (Final)
    2 Kernel \r on an \m
    3
    # 看到了吧！可以印出行号呢！这对于大档案要找某个特定的行时，有点用处！
    # 如果不想要编排空白行的行号，可以使用『cat -b /etc/issue』，自己测试看看：
    
范例三：将 /etc/xinetd.conf 的内容完整的显示出来(包含特殊字符)    
    
    [root@www ~]# cat -A /etc/xinetd.conf
    #$
    ....(中间省略)....
    $
    defaults$
    {$
    # The next two items are intended to be a quick access place to$
    ....(中间省略)....
    ^Ilog_type^I= SYSLOG daemon info $
    ^Ilog_on_failure^I= HOST$
    ^Ilog_on_success^I= PID HOST DURATION EXIT$
    
    ....(中间省略)....
    includedir /etc/xinetd.d$

## 二、Tac    
    
    [root@www ~]# tac /etc/issue
    
    Kernel \r on an \m
    CentOS release 5.3 (Final)
    # 嘿嘿！与刚刚上面的范例一比较，是由最后一行先显示喔！
    
tac 刚好是将 cat 反写过来，所以他的功能就跟cat 相反啦， cat 是由『第一行到最后一行连续显示在屏幕上』，而 tac 则是『 由最后一行到第一行反向在屏幕上显示出来 』

## 三、nl
    
    
    [root@www ~]# nl [-bnw] 档案
    选项与参数：
    -b ：指定行号指定的方式，主要有两种：
    -b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；
    -b t ：如果有空行，空的那一行不要列出行号(默认值)；
    -n ：列出行号表示的方法，主要有三种：
    -n ln ：行号在屏幕的最左方显示；
    -n rn ：行号在自己字段的最右方显示，且不加0 ；
    -n rz ：行号在自己字段的最右方显示，且加0 ；
    -w ：行号字段的占用的位数。
    
范例一：用 nl 列出 /etc/issue 的内容
    
    
    [root@www ~]# nl /etc/issue
    1 CentOS release 5.3 (Final)
    2 Kernel \r on an \m
    
    # 注意看，这个档案其实有三行，第三行为空白(没有任何字符)，
    # 因为他是空白行，所以 nl 不会加上行号喔！如果确定要加上行号，可以这样做：
    
    [root@www ~]# nl -b a /etc/issue
    1 CentOS release 5.3 (Final)
    2 Kernel \r on an \m
    3
    # 呵呵！行号加上来啰～那么如果要让行号前面自动补上 0 呢？可这样
   
    
    [root@www ~]# nl -b a -n rz /etc/issue
    000001 CentOS release 5.3 (Final)
    000002 Kernel \r on an \m
    000003
    # 嘿嘿！自动在自己字段的地方补上 0 了～预设字段是六位数，如果想要改成 3 位数？
    
    [root@www ~]# nl -b a -n rz -w 3 /etc/issue
    001 CentOS release 5.3 (Final)
    002 Kernel \r on an \m
    003
    # 变成仅有 3 位数啰～
    
## 四、More (一页一页翻动)

    
    [root@www ~]# more xxx
    空格键 (space)：代表向下翻一页；
    Enter ：代表向下翻『一行』；
    /字符串 ：代表在这个显示的内容当中，向下搜寻『字符串』这个关键词；
    :f ：立刻显示出文件名以及目前显示的行数；
    q ：代表立刻离开 more ，不再显示该档案内容。
    b 或 [ctrl]-b ：代表往回翻页，不过这动作只对档案有用，对管线无用。
    
## 五、less (一页一页翻动)

在 more 的时候，我们并没有办法向前面翻， 只能往后面看，但若使用了 less 时，呵呵
    
    空格键 ：向下翻动一页；
    [pagedown]：向下翻动一页；
    [pageup] ：向上翻动一页；
    /字符串 ：向下搜寻『字符串』的功能；
    ?字符串 ：向上搜寻『字符串』的功能；
    n ：重复前一个搜寻 (与 / 或 ? 有关！)
    N ：反向的重复前一个搜寻 (与 / 或 ? 有关！)
    q ：离开 less 这个程序；
    
## 六、head

head (取出前面几行)

    [root@linux ~]# head [-n number] 文件 

参数：

    -n  ：后面接数字，代表显示几行的意思

范例：

    [root@linux ~]# head /etc/man.config
    # 默认的情况中，显示前面十行！若要显示前 20 行，就得要这样：
    [root@linux ~]# head -n 20 /etc/man.config
     
     如果后面100行数据都不打印，只打印/etc/man.config前面几行，该如何是好呢
    [root@linux ~]# head -n -100 /etc/man.config
    
head 的英文意思就是『头』啦，那么这个东西的用法自然就是显示出一个档案的前几行啰！ 没错！就是这样！若没有加上 -n 这个参数时，默认只显示十行，若只要一行呢？那就加入『 head -n 1 filename 』即可！另外-n这个参数后面可以接一个负数，例如上面的，表示列出前面所有的行数，但不包括后面的的100行。，如果这个文件有141行的话，就只列出前面的41行，后面的100行就丢掉。


## 七、tail (取出后面几行)

    [root@linux ~]# tail [-n number] 文件

参数：

    -n  ：后面接数字，代表显示几行的意思

范例：

    [root@linux ~]# tail /etc/man.config
    # 默认的情况中，显示最后的十行！若要显示最后的 20 行，就得要这样：
    [root@linux ~]# tail -n 20 /etc/man.config

    如果不知道/etc/man.config有几行，却指向列出100行以后的数据时呢？（包括第100行被列出来）
    [root@linux ~]# tail -n +100 /etc/man.config

如果持续监测/var/log/messages的内容

    [root@linux ~]# tail -f /var/log/messages   #要输入Ctrl+c之后才会离开这个检测命令

那么有 head 自然就有 tail ( 尾巴 ) 啰！没错！这个 tail 的用法跟 head 的用法差不多类似，只是显示的是后面几行就是了！默认也是显示十行，若要显示非十行，就加 -n number 的参数！

例题一：假如我想要显示 ~/.bashrc 的第 11 到第 20 行呢？
答：
这个应该不算难，想一想，在第 11 到第 20 行，那么我取前 20 行，再取后十行，所以结果就是：

    head –n 20 ~/.bashrc | tail –n 10 ，
    这样就可以得到第 11 到第 20 行之间的内容了！ 但是里面涉及到管线命令，需要在第三篇的时候才讲的到！

**参考资料：**

鸟哥私房菜 http://linux.vbird.org/linux_basic/0220filemanager.php#more

http://linux.vbird.org/linux_basic/0220filemanager/0220filemanager-fc4.php#file_content_3
