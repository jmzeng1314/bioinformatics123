---
author: sam
comments: true
date: 2017-07-04 14:46:05+00:00
#layout: post
link: http://hugo.qinqianshan.com/the-judgment-basis-of-linux-command-execution/
slug: the-judgment-basis-of-linux-command-execution
title: Linux【3】-重定向与管道3-Linux命令执行的判断依据（;,&&,||）
wordpress_id: 2004
categories:
- basic_description
tags:
- '&amp;&amp;'
- '||'
---

在某些情况下，很多指令我想要一次输入去执行，而不想要分次执行时，该如何是好？基本上你有两个选择， 一个是透过的 shell script 撰写脚本去执行，一种则是透过底下的介绍来一次输入多重指令喔！

<!-- more -->

## 一、cmd ; cmd (不考虑指令相关性的连续指令下达)

在某些时候，我们希望可以一次执行多个指令，例如在关机的时候我希望可以先执行两次 sync 同步化写入磁盘后才shutdown 计算机，那么可以怎么作呢？这样做呀：
    
    [root@www ~]# sync; sync; shutdown -h now

在指令与指令中间利用分号(;) 来隔开，这样一来，分号前的指令执行完后就会立刻接着执行后面的指令了。 这真是方便啊～再来，换个角度来想，万一我想要在某个目录底下建立一个文件，也就是说，如果该目录存在的话， 那我才建立这个文件，如果不存在，那就算了。也就是说这两个指令彼此之间是有相关性的， 前一个指令是否成功的执行与后一个指令是否要执行有关！那就得动用到 && 或 || 啰！

## 二、$? (指令回传值) 与 && 或 ||

如同上面谈到的，两个指令之间有相依性，而这个相依性主要判断的地方就在于前一个指令执行的结果是否正确。 还记得本章之前我们曾介绍过指令回传值吧！嘿嘿！没错，您真聪明！就是透过这个回传值啦！ 再复习一次『若前一个指令执行的结果为正确，在 Linux 底下会回传一个 $? = 0 的值』。 那么我们怎么透过这个回传值来判断后续的指令是否要执行呢？这就得要藉由『 && 』及『 || 』的帮忙了！ 注意喔，两个 & 之间是没有空格的！那个 | 则是[Shift]+[\] 的按键结果。

指令下达情况说明
    
cmd1 && cmd2

    1. 若 cmd1 执行完毕且正确执行($?=0)，则开始执行 cmd2。
    2. 若 cmd1 执行完毕且为错误 ($?≠0)，则 cmd2 不执行。

cmd1 || cmd2

    1. 若 cmd1 执行完毕且正确执行($?=0)，则 cmd2 不执行。
    2. 若 cmd1 执行完毕且为错误 ($?≠0)，则开始执行 cmd2。

上述的 cmd1 及 cmd2都是指令。好了，回到我们刚刚假想的情况，就是想要：
 
 (1)先判断一个目录是否存在；
 
 (2)若存在才在该目录底下建立一个文件。

 由于我们尚未介绍如何判断式 (test) 的使用，在这里我们使用ls 以及回传值来判断目录是否存在啦！ 让我们进行底下这个练习看看：

**范例一：**

使用 ls 查阅目录 /tmp/abc 是否存在，若存在则用 touch 建立 /tmp/abc/hehe
    
    [root@www ~]# ls /tmp/abc && touch /tmp/abc/hehe
    ls: /tmp/abc: No such file or directory

ls 很干脆的说明找不到该目录，但并没有 touch 的错误，表示 touch 并没有执行
    
    [root@www ~]# mkdir /tmp/abc
    [root@www ~]# ls /tmp/abc && touch /tmp/abc/hehe
    [root@www ~]# ll /tmp/abc
    -rw-r--r-- 1 root root 0 Feb 7 12:43 hehe

看到了吧？如果 /tmp/abc 不存在时，touch 就不会被执行，若 /tmp/abc 存在的话，那么 touch 就会开始执行啰！ 很不错用吧！不过，我们还得手动自行建立目录，伤脑筋～能不能自动判断，如果没有该目录就给予建立呢？ 参考一下底下的例子先：

**范例二：**

测试 /tmp/abc 是否存在，若不存在则予以建立，若存在就不作任何事情
    
    [root@www ~]# rm -r /tmp/abc <==先删除此目录以方便测试
    [root@www ~]# ls /tmp/abc || mkdir /tmp/abc
    ls: /tmp/abc: No such file or directory <==真的不存在喔！
    [root@www ~]# ll /tmp/abc

total 0 <==结果出现了！有进行 mkdir

如果你一再重复『 ls /tmp/abc || mkdir /tmp/abc 』画面也不会出现重复 mkdir 的错误！这是因为/tmp/abc 已经存在， 所以后续的 mkdir 就不会进行！这样理解否？好了，让我们再次的讨论一下，如果我想要建立 /tmp/abc/hehe 这个文件， 但我并不知道 /tmp/abc 是否存在，那该如何是好？试看看：

**范例三：**

我不清楚 /tmp/abc 是否存在，但就是要建立/tmp/abc/hehe 文件
    
    [root@www ~]#ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe

上面这个范例三总是会建立 /tmp/abc/hehe 的喔！不论/tmp/abc 是否存在。那么范例三应该如何解释呢？ 由于Linux 底下的指令都是由左往右执行的，所以范例三有几种结果我们来分析一下：

(1)若 /tmp/abc 不存在故回传 $?≠0，则 (2)因为 || 遇到非为 0 的 $? 故开始 mkdir /tmp/abc，由于 mkdir /tmp/abc 会成功进行，所以回传 $?=0 (3)因为 && 遇到 $?=0 故会执行 touch /tmp/abc/hehe，最终 hehe 就被建立了；

(1)若 /tmp/abc 存在故回传 $?=0，则 (2)因为 || 遇到 0 的 $? 不会进行，此时 $?=0 继续向后传，故 (3)因为 && 遇到 $?=0 就开始建立 /tmp/abc/hehe 了！最终/tmp/abc/hehe 被建立起来。

上面这张图显示的两股数据中，上方的线段为不存在 /tmp/abc 时所进行的指令行为，下方的线段则是存在 /tmp/abc 所在的指令行为。如上所述，下方线段由于存在 /tmp/abc 所以导致 $?=0 ，让中间的 mkdir 就不执行了！ 并将 $?=0 继续往后传给后续的touch 去利用啦！瞭乎？在任何时刻你都可以拿上面这张图作为示意！ 让我们来想想底下这个例题吧！

例题：

以 ls 测试/tmp/vbirding 是否存在，若存在则显示"exist" ，若不存在，则显示"not exist"！

答：

这又牵涉到逻辑判断的问题，如果存在就显示某个数据，若不存在就显示其他数据，那我可以这样做：
    
    ls /tmp/vbirding && echo "exist" || echo "not exist"

意思是说，当 ls /tmp/vbirding 执行后，若正确，就执行echo "exist" ，若有问题，就执行echo "not exist" ！那如果写成如下的状况会出现什么？
    
    ls /tmp/vbirding || echo "not exist" && echo "exist"

这其实是有问题的，为什么呢？由图 5.2.1 的流程介绍我们知道指令是一个一个往后执行， 因此在上面的例子当中，如果 /tmp/vbirding 不存在时，他会进行如下动作：

若 ls /tmp/vbirding 不存在，因此回传一个非为0 的数值；

接下来经过 || 的判断，发现前一个指令回传非为 0 的数值，因此，程序开始执行echo "not exist" ，而 echo "not exist" 程序肯定可以执行成功，因此会回传一个 0 值给后面的指令；

经过 && 的判断，咦！是 0 啊！所以就开始执行 echo "exist" 。

所以啊，嘿嘿！第二个例子里面竟然会同时出现 not exist 与 exist 呢！真神奇～

经过这个例题的练习，你应该会了解，由于指令是一个接着一个去执行的，因此，如果真要使用判断， 那么这个 && 与 || 的顺序就不能搞错。一般来说，假设判断式有三个，也就是：
    
    command1 && command2 || command3

而且顺序通常不会变，因为一般来说， command2 与 command3 会放置肯定可以执行成功的指令， 因此，依据上面例题的逻辑分析，您就会晓得为何要如此放置啰～这很有用的啦！而且.....考试也很常考～

**参考资料：**

鸟哥私房菜： http://linux.vbird.org/linux_basic/0320bash.php#redirect_com
