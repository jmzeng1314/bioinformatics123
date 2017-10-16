---
author: sam
comments: true
date: 2017-07-13 07:18:20+00:00
#layout: post
link: http://hugo.qinqianshan.com/cat-uniq-split-paste
slug: cat-uniq-split-paste
title: Linux【2】-管理文件14-linux文件的合并，去重，分割(cat/uniq/split/paste)
wordpress_id: 1466
categories:
- basic_description
tags:
- cat
- split
- uniq
- paste
---

<blockquote>最近Sam有些蓝瘦、香菇，不停的在重复着一些文本操作的命令，有一些小的快捷的技能整理一下。
<!-- more --></blockquote>

## 一、两个文件的交集，并集

前提条件：每个文件中不得有重复行
1. 取出两个文件的并集(重复的行只保留一份)
    
    cat file1 file2 | sort | uniq > file3

2. 取出两个文件的交集(只留下同时存在于两个文件中的文件)
    
    cat file1 file2 | sort | uniq -d > file3

3. 删除交集，留下其他的行
    
    cat file1 file2 | sort | uniq -u > file3

## 二、两个文件合并
一个文件在上，一个文件在下
    
    cat file1 file2 > file3

一个文件在左，一个文件在右
    
    paste file1 file2 > file3


## 三、paste详解：
    
    [root@www ~]# paste [-d] file1 file2
    选项与参数：
    -d  ：后面可以接分隔字符。默认是以 [tab] 来分隔的！
    -   ：如果 file 部分写成 - ，表示来自 standard input 的数据的意思。
     
范例一：将 /etc/passwd 与 /etc/shadow 同一行贴在一起
    
    [root@www ~]# paste /etc/passwd /etc/shadow
    bin:x:1:1:bin:/bin:/sbin/nologin        bin:*:14126:0:99999:7:::
    daemon:x:2:2:daemon:/sbin:/sbin/nologin daemon:*:14126:0:99999:7:::
    adm:x:3:4:adm:/var/adm:/sbin/nologin    adm:*:14126:0:99999:7:::
    # 注意喔！同一行中间是以 [tab] 按键隔开的！
     
范例二：先将 /etc/group 读出(用 cat)，然后与范例一贴上一起！且仅取出前三行
    
    [root@www ~]# cat /etc/group|paste /etc/passwd /etc/shadow -|head -n 3
    # 这个例子的重点在那个 - 的使用！那玩意儿常常代表 stdin 喔！
    
例：使用chpasswd批量修改用户密码

    [root@localhost ~]# cat name 
    user1:
    user2:
    user3:
    user4:
    user5:
    user6:
    user7:
    user8:
    user9:
    [root@localhost ~]# cat passwd 
    978ebbf1763
    23c2626c37b
    15dff03fa71
    2459043b3ad
    d317ec4870c
    1728b6b5ddc
    ed9d98f8b4c
    0a7f3f5f8c4
    96935ad635c

    [root@localhost ~]# paste name passwd 
    user1:    978ebbf1763
    user2:    23c2626c37b
    user3:    15dff03fa71
    user4:    2459043b3ad
    user5:    d317ec4870c
    user6:    1728b6b5ddc
    user7:    ed9d98f8b4c
    user8:    0a7f3f5f8c4
    user9:    96935ad635c

    [root@localhost ~]# paste -d"#" name passwd 
    user1:#978ebbf1763
    user2:#23c2626c37b
    user3:#15dff03fa71
    user4:#2459043b3ad
    user5:#d317ec4870c
    user6:#1728b6b5ddc
    user7:#ed9d98f8b4c
    user8:#0a7f3f5f8c4
    user9:#96935ad635c
    注：-d指定分隔符，不加此参数默认为制表符。
    
## 三：一个文件去掉重复的行：

    sort file |uniq

注意：重复的多行记为一行，也就是说这些重复的行还在，只是全部省略为一行！
    
    sort file |uniq -u

上面的命令可以把重复的行全部去掉，也就是文件中的非重复行！

具体细节可以查看，cat，sort，uniq等命令的详细介绍

## 四：将一个大的文件分割成多个小文件：

采用一个50M大小的日志文件进行测试。
日志文件名：log.txt.gz。
文件行数：208363

**方法1：split分割(这个专业额)**
    
    语法：split [-<行数>][-b <字节>][-C <字节>][-l <行数>][要切割的文件][输出文件名]
    -b  ：後面可接欲分割成的檔案大小，可加單位，例如 b, k, m 等；
    -l  ：以行數來進行分割。    
    
    # gunzip log.txt.gz             //一定要先解压，否则分割的文件是不能cat/zcat显示；
    # wc -l log.txt                 //计算一个文件的总行数；
    
    208363 log.txt
    # split -l 120000 log.txt newlog    //通过指定行数，将日志分割成两个文件；
    # du -sh *50M     log.txt
    29M     newlogaa
    22M     newlogab
    # file *                         //分割后的文件与原文件属性一样
    log.txt: ASCII text, with very long lines, with CRLF line terminators
    newlogaa: ASCII text, with very long lines, with CRLF line terminators
    newlogab: ASCII text, with very long lines, with CRLF line terminators
    # gzip newlogaa newlogab         //将分割后的文件进行压缩，以便传输
    
    [root@www tmp]# ls -al / | split -l 10 - lsroot
    [root@www tmp]# wc -l lsroot*
      10 lsrootaa
      10 lsrootab
       6 lsrootac
      26 total
    
重點在那個 - 啦！一般來說，如果需要 stdout/stdin 時，但偏偏又沒有檔案，
有的只是 - 時，那麼那個 - 就會被當成 stdin 或 stdout ～

**方法2：dd分割**

    # gunzip log.txt.gz             //一定要先解压，否则分割的文件是不能cat/zcat显示；
    #dd bs=20480 count=1500 if=log.txt of=newlogaa      //按大小分第一个文件
    
    #dd bs=20480 count=1500 if=log.txt of=newlogab skip=1500  //将大小之后的生成另一个文件#file *
    
    log.txt: ASCII text, with very long lines, with CRLF line terminators
    newlogaa: ASCII text, with very long lines, with CRLF line terminators
    newlogab: ASCII text, with very long lines, with CRLF line terminators
    
分割没问题，但会出现同一行分到不同文件的情况，除非你以及日志分析系统可以“容忍”。
很简单，因为这个的分割是根据字符数来分割的。
if=filename：输入的文件名
of=finename：输出的文件名
bs=bytes：一次读写的字节数，默认是512bytes
skip=blocks:拷贝前，跳过的输入文件的前blocks块，块的大小由bs决定，意思就是从哪个块为起点。
count=blocks：只拷贝输入文件的前blocks块
上面的例子中是把原文件切割成20K大小的块，前1500块放在文件newlogaa，后面的放在文件newlogab。

**方法3：head+tail 分割**
    
    #gzip log.txt.gz               //如不解压缩，下面请用zcat。
    #wc -l log.txt                //统计一个行数
    208363 log.txt
    # head -n `echo $((208363/2+1))` log.txt > newloga.txt      //前x行重定向输出到一个文件中；
    #tail –n `echo $((208363-208362/2-1))` log.txt >newlogb.txt //后x行重定向输出到一个文件中；
    #gzip newloga.txt newlogb.txt          //将两个文件进行压缩
    
**方法4：awk分割**

    #gzip log.txt.gz#awk ‘{if (NR<120000) print $0}’ log.txt >newloga.txt#awk ‘{if (NR>=120000) print $0}’ log.txt >newlogb.txt
     
    以上两个命令，都要遍历整个文件，所以考虑到效率，应使用合并成：
    #awk ‘{if (NR<120000) print $0 >”newloga.txt”;if (NR>=120000) print $0>”newlogb.txt”}’ log.txt
    
以上四种方法，除了dd之外的三种方式都可以很好的整行分割日志文件。进行分割时，应考虑在读一次文件的同时完成，如不然，按下面的方式分割：
    
    Cat log.txt| head –12000 >newloga.txt
    Cat log.txt | tail –23000 >newlogb.txt
    
如用此方法分割文件的后一部分，那么执行第二行命令文件时，前x行是白白读一遍的，执行的效率将很差，如文件过大，还可能出现内存不够的情况。

**参考文献：**

长颈鹿Giraffe的博客 http://www.cnblogs.com/giraffe/p/3193085.html

Bob's Blog的博客 http://blog.csdn.net/exbob/article/details/6636255 （有不错的脚本）

鸟哥私房菜 http://linux.vbird.org/linux_basic/0320bash.php#paste
