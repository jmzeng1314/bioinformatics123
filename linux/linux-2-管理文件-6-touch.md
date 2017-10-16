---
author: sam
comments: true
date: 2017-08-25 15:52:48+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-touch/
slug: linux-touch
title: Linux【2】-管理文件6-修改文件时间或建置新档（touch）
wordpress_id: 3101
categories:
- basic_description
tags:
- touch
---

我们在 ls 这个指令的介绍时，有稍微提到每个文件在linux底下都会记录许多的时间参数， 其实是有三个主要的变动时间，那么三个时间的意义是什么呢？
<!-- more -->

    modification time (mtime)：
    当该文件的『内容数据』变更时，就会更新这个时间！内容数据指的是文件的内容，而不是文件的属性或权限喔！
    
    status time (ctime)：
    当该文件的『状态 (status)』改变时，就会更新这个时间，举例来说，像是权限与属性被更改了，都会更新这个时间啊。
    
    access time (atime)：
    当『该文件的内容被取用』时，就会更新这个读取时间 (access)。举例来说，我们使用 cat 去读取 /etc/man.config ， 就会更新该文件的 atime 了。

这是个挺有趣的现象，举例来说，我们来看一看你自己的 /etc/man.config 这个文件的时间吧！

    [root@www ~]# ls -l /etc/man.config
    -rw-r--r-- 1 root root 4617 Jan  6  2007 /etc/man.config
    
    [root@www ~]# ls -l --time=atime /etc/man.config
    -rw-r--r-- 1 root root 4617 Sep 25 17:54 /etc/man.config
    
    [root@www ~]# ls -l --time=ctime /etc/man.config
    -rw-r--r-- 1 root root 4617 Sep  4 18:03 /etc/man.config

看到了吗？在默认的情况下，ls 显示出来的是该文件的mtime ，也就是这个文件的内容上次被更动的时间。 至于鸟哥的系统是在 9 月 4 号的时候安装的，因此，这个文件被产生导致状态被更动的时间就回溯到那个时间点了(ctime)！ 而还记得刚刚我们使用的范例当中，有使用到man.config这个文件啊，所以啊，他的 atime 就会变成刚刚使用的时间了！

文件的时间是很重要的，因为，如果文件的时间误判的话，可能会造成某些程序无法顺利的运作。 OK！那么万一我发现了一个文件来自未来，该如何让该文件的时间变成『现在』的时刻呢？ 很简单啊！就用『touch』这个指令即可！

Tips:

嘿嘿！不要怀疑系统时间会『来自未来』喔！很多时候会有这个问题的！举例来说在安装过后系统时间可能会被改变！ 因为台湾时区在国际标准时间『格林威治时间, GMT』的右边，所以会比较早看到阳光，也就是说，台湾时间比GMT时间快了八小时！ 如果安装行为不当，我们的系统可能会有八小时快转，你的文件就有可能来自八小时后了。

至于某些情况下，由于BIOS的设定错误，导致系统时间跑到未来时间，并且你又建立了某些文件。 等你将时间改回正确的时间时，该文件不就变成来自未来了？^_^       鸟哥的图示

    [root@www ~]# touch [-acdmt] 文件

选项与参数：

    -a  ：仅修订 access time；
    -c  ：仅修改文件的时间，若该文件不存在则不建立新文件；
    -d  ：后面可以接欲修订的日期而不用目前的日期，也可以使用 --date="日期或时间"
    -m  ：仅修改 mtime ；
    -t  ：后面可以接欲修订的时间而不用目前的时间，格式为[YYMMDDhhmm]

范例一：新建一个空的文件并观察时间

    [root@www ~]# cd /tmp
    [root@www tmp]# touch testtouch
    [root@www tmp]# ls -l testtouch
    
    -rw-r--r-- 1 root root 0 Sep 25 21:09 testtouch

注意到，这个文件的大小是0 呢！在预设的状态下，如果 touch 后面有接文件，
则该文件的三个时间(atime/ctime/mtime) 都会更新为目前的时间。若该文件不存在，
则会主动的建立一个新的空的文件喔！例如上面这个例子！

范例二：将 ~/.bashrc 复制成为 bashrc，假设复制完全的属性，检查其日期

    [root@www tmp]# cp -a ~/.bashrc bashrc    
    [root@www tmp]# ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
    
    -rw-r--r-- 1 root root 176 Jan  6  2007 bashrc  <==这是 mtime    
    -rw-r--r-- 1 root root 176 Sep 25 21:11 bashrc  <==这是 atime   
    -rw-r--r-- 1 root root 176 Sep 25 21:12 bashrc  <==这是 ctime

在上面这个案例当中我们使用了『ll』这个指令(两个英文L的小写)，这个指令其实就是『ls -l』的意思， ll本身不存在，是被『做出来』的一个命令别名。相关的命令别名我们会在bash章节当中详谈的，这里先知道ll="ls -l"即可。 至于分号『 ; 』则代表连续指令的下达啦！你可以在一行指令当中写入多重指令， 这些指令可以『依序』执行。由上面的指令我们会知道ll那一行有三个指令被下达在同一行中。

至于执行的结果当中，我们可以发现数据的内容与属性是被复制过来的，因此文件内容时间(mtime)与原本文件相同。 但是由于这个文件是刚刚被建立的，因此状态(ctime)与读取时间就便呈现在的时间啦！ 那如果你想要变更这个文件的时间呢？可以这样做：

 

范例三：修改案例二的 bashrc 文件，将日期调整为两天前

    [root@www tmp]# touch -d "2 days ago" bashrc
    
    [root@www tmp]# ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
    -rw-r--r-- 1 root root 176 Sep 23 21:23 bashrc
    -rw-r--r-- 1 root root 176 Sep 23 21:23 bashrc
    -rw-r--r-- 1 root root 176 Sep 25 21:23 bashrc

跟上个范例比较看看，本来是25 日的变成了 23 日了 (atime/mtime)～
不过， ctime 并没有跟着改变喔！


范例四：将上个范例的 bashrc 日期改为2007/09/15 2:02

    [root@www tmp]# touch -t 0709150202 bashrc
    
    [root@www tmp]# ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
    
    -rw-r--r-- 1 root root 176 Sep 15  2007 bashrc
    -rw-r--r-- 1 root root 176 Sep 15  2007 bashrc
    -rw-r--r-- 1 root root 176 Sep 25 21:25 bashrc
注意看看，日期在atime 与 mtime 都改变了，但是 ctime 则是记录目前的时间！

透过 touch 这个指令，我们可以轻易的修订文件的日期与时间。并且也可以建立一个空的文件喔！ 不过，要注意的是，即使我们复制一个文件时，复制所有的属性，但也没有办法复制 ctime 这个属性的。 ctime 可以记录这个文件最近的状态 (status) 被改变的时间。无论如何，还是要告知大家， 我们平时看的文件属性中，比较重要的还是属于那个 mtime 啊！我们关心的常常是这个文件的『内容』 是什么时候被更动的说～瞭乎？

 

无论如何， touch 这个指令最常被使用的情况是：

    建立一个空的文件；
    将某个文件日期修订为目前 (mtime 与 atime)


**参考资料：**

鸟哥私房菜：http://linux.vbird.org/linux_basic/0220filemanager.php#touch
