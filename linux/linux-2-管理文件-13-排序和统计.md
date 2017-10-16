---
author: sam
comments: true
date: 2017-07-14 14:45:53+00:00
#layout: post
link: http://hugo.qinqianshan.com/some-sort-of-statistical-directives-under-linux-sortwcuniq/
slug: some-sort-of-statistical-directives-under-linux-sortwcuniq
title: Linux【2】-管理文件13-Linux排序与统计指令（sort,wc,uniq）
wordpress_id: 1529
categories:
- basic_description
tags:
- sort
- uniq
- wc
---

很多时候，我们都会去计算一次数据里头的相同型态的数据总数，举例来说， 使用 last 可以查得这个月份有登入主机者的身份。那么我可以针对每个使用者查出他们的总登入次数吗？ 此时就得要排序与计算之类的指令来辅助了！底下我们介绍几个好用的排序与统计指令喔！
<!-- more -->

## 一、sort

sort 是很有趣的指令，他可以帮我们进行排序，而且可以依据不同的数据型态来排序喔！ 例如数字与文字的排序就不一样。此外，排序的字符与语系的编码有关，因此， 如果您需要排序时，建议使用 LANG=C 来让语系统一，数据排序比较好一些。
    
[root@www ~]# sort [-fbMnrtuk] [file or stdin]

    选项与参数：
    -f ：忽略大小写的差异，例如 A 与 a 视为编码相同；
    -b ：忽略最前面的空格符部分；
    -M ：以月份的名字来排序，例如 JAN, DEC 等等的排序方法；
    -n ：使用『纯数字』进行排序(默认是以文字型态来排序的)；
    -r ：反向排序；
    -u ：就是 uniq ，相同的数据中，仅出现一行代表；
    -t ：分隔符，预设是用 [tab] 键来分隔；
    -k ：以那个区间 (field) 来进行排序的意思
    
范例一：个人账号都记录在 /etc/passwd 下，请将账号进行排序。
    
    [root@www ~]# cat /etc/passwd | sort
    adm:x:3:4:adm:/var/adm:/sbin/nologin
    apache:x:48:48:Apache:/var/www:/sbin/nologin
    bin:x:1:1:bin:/bin:/sbin/nologin
    daemon:x:2:2:daemon:/sbin:/sbin/nologin
    # 鸟哥省略很多的输出～由上面的数据看起来， sort 是预设『以第一个』数据来排序，
    # 而且默认是以『文字』型态来排序的喔！所以由 a 开始排到最后啰！
    
范例二：/etc/passwd 内容是以 : 来分隔的，我想以第三栏来排序，该如何？
    
    [root@www ~]# cat /etc/passwd | sort -t ':' -k 3
    root:x:0:0:root:/root:/bin/bash
    uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
    operator:x:11:0:operator:/root:/sbin/nologin
    bin:x:1:1:bin:/bin:/sbin/nologin
    games:x:12:100:games:/usr/games:/sbin/nologin
    # 看到特殊字体的输出部分了吧？怎么会这样排列啊？呵呵！没错啦～
    # 如果是以文字型态来排序的话，原本就会是这样，想要使用数字排序：
    # cat /etc/passwd | sort -t ':' -k 3 -n
    # 这样才行啊！用那个 -n 来告知 sort 以数字来排序啊！
    
范例三：利用 last ，将输出的数据仅取账号，并加以排序
    
    [root@www ~]# last | cut -d ' ' -f1 | sort
    
sort 同样是很常用的指令呢！因为我们常常需要比较一些信息啦！ 举个上面的第二个例子来说好了！今天假设你有很多的账号，而且你想要知道最大的使用者 ID 目前到哪一号了！呵呵！使用 sort 一下子就可以知道答案咯！当然其使用还不止此啦！ 有空的话不妨玩一玩！

常用的合并后排序
    
    cat /home/products/* >total.snps
    sort total.snps|uniq -c |sort -r -t ' ' -k 2 |head
    
### 讨论

**问题1：**

    sort -t: 4 order.adapter.list >1.order.adapter.list

后来一直报错：sort: 打开失败: +1: 没有那个文件或目录

解决办法：

一查原来是因为我的这个命令比较老了，所以需要进行修改

    sort -t: -k 5 -g adapter.list >3.order.adapter.list

上面的才是正确的命令：

 数字大小比较可以用-g的参数代替-n的参数
第四个冒号后的数字是按冒号分开的第5列，应该是-k 5

## 二、uniq

如果我排序完成了，想要将重复的资料仅列出一个显示，可以怎么做呢？
    
    [root@www ~]# uniq [-ic]
    选项与参数：
    -i ：忽略大小写字符的不同；
    -c ：进行计数
    
范例一：使用 last 将账号列出，仅取出账号栏，进行排序后仅取出一位；

    [root@www ~]# last | cut -d ' ' -f1 | sort | uniq
    
范例二：承上题，如果我还想要知道每个人的登入总次数呢？

    [root@www ~]# last | cut -d ' ' -f1 | sort | uniq -c
    1
    12 reboot
    41 root
    1 wtmp
    # 从上面的结果可以发现 reboot 有 12 次， root 登入则有 41 次！
    # wtmp 与第一行的空白都是 last 的默认字符，那两个可以忽略的！
    
这个指令用来将『重复的行删除掉只显示一个』，举个例子来说， 你要知道这个月份登入你主机的用户有谁，而不在乎他的登入次数，那么就使用上面的范例， (1)先将所有的数据列出；(2)再将人名独立出来；(3)经过排序；(4)只显示一个！ 由于这个指令是在将重复的东西减少，所以当然需要『配合排序过的档案』来处理啰！
    
    1. 取出两个文件的并集(重复的行只保留一份)
    2. 取出两个文件的交集(只留下同时存在于两个文件中的文件)
    3. 删除交集，留下其他的行
    1. cat file1 file2 | sort | uniq > file3
    2. cat file1 file2 | sort | uniq -d > file3
    3. cat file1 file2 | sort | uniq -u > file3
    
## 三、wc

如果我想要知道 /etc/man.config 这个档案里面有多少字？多少行？多少字符的话， 可以怎么做呢？其实可以利用 wc 这个指令来达成喔！他可以帮我们计算输出的讯息的整体数据！
    
    [root@www ~]# wc [-lwm]
    选项与参数：
    -l ：仅列出行；
    -w ：仅列出多少字(英文单字)；
    -m ：多少字符；
    
范例一：那个 /etc/man.config 里面到底有多少相关字、行、字符数？
    
    [root@www ~]# cat /etc/man.config | wc
    141 722 4617
    # 输出的三个数字中，分别代表： 『行、字数、字符数』
    
范例二：我知道使用 last 可以输出登入者，但是 last 最后两行并非账号内容，
那么请问，我该如何以一行指令串取得这个月份登入系统的总人次？
    
    [root@www ~]# last | grep [a-zA-Z] | grep -v 'wtmp' | wc -l
    # 由于 last 会输出空白行与 wtmp 字样在最底下两行，因此，我利用
    # grep 取出非空白行，以及去除 wtmp 那一行，在计算行数，就能够了解啰！
    
wc 也可以当作指令？这可不是上洗手间的 WC 呢！这是相当有用的计算档案内容的一个工具组喔！举个例子来说， 当你要知道目前你的账号档案中有多少个账号时，就使用这个方法：『 cat /etc/passwd | wc -l 』啦！因为 /etc/passwd 里头一行代表一个使用者呀！ 所以知道行数就晓得有多少的账号在里头了！而如果要计算一个档案里头有多少个字符时，就使用 wc -m 这个选项吧！

**参考资料:**

鸟哥linux私房菜cut linux.vbird.org/linux_basic/0320bash.php#cut
