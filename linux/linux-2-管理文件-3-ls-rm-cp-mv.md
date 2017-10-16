---
author: sam
comments: true
date: 2017-09-02 08:41:42+00:00
#layout: post
link: http://hugo.qinqianshan.com/ls-rm-cp-mv/
slug: ls-rm-cp-mv
title: Linux【2】-管理文件3-ls/rm/cp/mv
wordpress_id: 1032
categories:
- basic_description
tags:
- cp
- ls
- mv
- rm
---

今天心里空落落的，来复习一下常用的几个文件与目录管理命令：ls、rm、cp、mv的参数吧。
<!-- more -->

## 一、ls

    [root@www ~]# ls [-aAdfFhilnrRSt] 目录名称
    [root@www ~]# ls [--color={never,auto,always}] 目录名称
    [root@www ~]# ls [--full-time] 目录名称

    选项与参数：
     -a ：全部的档案，连同隐藏档( 开头为 . 的档案) 一起列出来(常用)
     -A ：全部的档案，连同隐藏档，但不包括 . 与 .. 这两个目录
     -d ：仅列出目录本身，而不是列出目录内的档案数据(常用)
     -f ：直接列出结果，而不进行排序 (ls 预设会以档名排序！)
     -F ：根据档案、目录等信息，给予附加数据结构，例如：
     *:代表可执行文件； /:代表目录； =:代表 socket 档案； |:代表 FIFO 档案；
     -h ：将档案容量以人类较易读的方式(例如 GB, KB 等等)列出来；
     -i ：列出 inode 号码，inode 的意义下一章将会介绍；
     -l ：长数据串行出，包含档案的属性与权限等等数据；(常用)
     -n ：列出 UID 与 GID 而非使用者与群组的名称(UID与GID会在账号管理提到！)
     -r ：将排序结果反向输出，例如：原本档名由小到大，反向则为由大到小；
     -R ：连同子目录内容一起列出来，等于该目录下的所有档案都会显示出来；
     -S ：以档案容量大小排序，而不是用档名排序；
     -t ：依时间排序，而不是用档名。
     --color=never ：不要依据档案特性给予颜色显示；<strong>默认的有执行权限的为绿色字体，文件夹为绿色背景</strong>
     --color=always ：显示颜色
     --color=auto ：让系统自行依据设定来判断是否给予颜色
     --full-time ：以完整时间模式 (包含年、月、日、时、分) 输出
     --time={atime,ctime} ：输出 access 时间或改变权限属性时间(ctime)
     而非内容变更时间(modification time)
     只有下达 ls 时，默认显示的只有：非隐藏档的档名、以档名进行排序及文件名代表的颜色显示如此而已

## 二、cp

    [root@www ~]# cp [-adfilprsu] 来源文件(source) 目标文件(destination)
    [root@www ~]# cp [options] source1 source2 source3 .... directory

    
    选项与参数：
     -a ：相当于 -pdr 的意思，至于 pdr 请参考下列说明；(常用)
     -d ：若来源文件为链接文件的属性(link file)，则复制链接文件属性而非档案本身；
     -f ：为强制(force)的意思，若目标档案已经存在且无法开启，则移除后再尝试一次；
     -i ：若目标文件(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
     -l ：进行硬式连结(hard link)的连结档建立，而非复制档案本身；
     -p ：连同档案的属性一起复制过去，而非使用默认属性(备份常用)；
     -r ：递归持续复制，用于目录的复制行为；(常用)
     -s ：复制成为符号链接文件(symbolic link)，亦即
     档案；
     -u ：若destination 比 source 旧才更新 destination ！
     最后需要注意的，如果来源档有两个以上，则最后一个目标文档一定要是『目录』才行！

## 三、rm

    [root@www ~]# rm [-fir] 档案或目录
    
    选项与参数：
     -f ：就是 force 的意思，忽略不存在的档案，不会出现警告讯息；
     -i ：互动模式，在删除前会询问使用者是否动作
     -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！

范例一：将刚刚在 cp 的范例中建立的 bashrc 删除掉！

    [root@www ~]# cd /tmp
    [root@www tmp]# rm -i bashrc
    rm: remove regular file `bashrc'? y

如果加上 -i 的选项就会主动询问喔，避免你删除到错误的档名！

范例二：透过通配符*的帮忙，将/tmp底下开头为bashrc的档名通通删除：

    [root@www tmp]# rm -i bashrc*

注意那个星号，代表的是 0 到无穷多个任意字符喔！很好用的东西！

范例三：将 cp 范例中所建立的 /tmp/etc/ 这个目录删除掉！

    [root@www tmp]# rmdir /tmp/etc
    rmdir: etc: Directory not empty <== 删不掉啊！因为这不是空的目录！
    [root@www tmp]# rm -r /tmp/etc
    rm: descend into directory `/tmp/etc'? y
    ....(中间省略)....

因为身份是 root ，预设已经加入了 -i 的选项，所以你要一直按 y 才会删除！
如果不想要继续按 y ，可以按下『[ctrl]-c 』来结束 rm 的工作。
这是一种保护的动作，如果确定要删除掉此目录而不要询问，可以这样做：

    [root@www tmp]# \rm -r /tmp/etc

在指令前加上反斜杠，可以忽略掉 alias 的指定选项喔！至于 alias 我们在bash再谈！

范例四：删除一个带有 - 开头的档案

    [root@www tmp]# touch ./-aaa- <==touch这个指令可以建立空档案！
    [root@www tmp]# ls -l
    -rw-r--r-- 1 root root 0 Sep 24 15:03 -aaa- <==档案大小为0，所以是空档案
    [root@www tmp]# rm -aaa-
    Try `rm --help' for more information. <== 因为 "-" 是选项嘛！所以系统误判了！
    [root@www tmp]# rm ./-aaa-

## 四、mv

    [root@www ~]# mv [-fiu] source destination
    [root@www ~]# mv [options] source1 source2 source3 .... directory
    
    选项与参数：
     -f ：force 强制的意思，如果目标档案已经存在，不会询问而直接覆盖；
     -i ：若目标档案(destination) 已经存在时，就会询问是否覆盖！
     -u ：若目标档案已经存在，且source 比较新，才会更新 (update)

范例一：复制一档案，建立一目录，将档案移动到目录中

    [root@www ~]# cd /tmp
    [root@www tmp]# cp ~/.bashrc bashrc
    [root@www tmp]# mkdir mvtest
    [root@www tmp]# mv bashrc mvtest

将某个档案移动到某个目录去，就是这样做！

范例二：将刚刚的目录名称更名为 mvtest2

    [root@www tmp]# mv mvtest mvtest2 <== 这样就更名了！简单～

其实在 Linux 底下还有个有趣的指令，名称为 rename ，
该指令专职进行多个档名的同时更名，并非针对单一档名变更，与mv不同。请man rename。

范例三：再建立两个档案，再全部移动到 /tmp/mvtest2 当中
    
    [root@www tmp]# cp ~/.bashrc bashrc1
    [root@www tmp]# cp ~/.bashrc bashrc2
    [root@www tmp]# mv bashrc1 bashrc2 mvtest2

注意到这边，如果有多个来源档案或目录，则最后一个目标文件一定是『目录！』
意思是说，将所有的数据移动到该目录的意思！

*取得路径的文件名与目录名称*

    [root@www ~]# basename /etc/sysconfig/network
    network <== 很簡單！就取得最後的檔名～
    [root@www ~]# dirname /etc/sysconfig/network
    /etc/sysconfig <== 取得的變成目錄名了！

**参考资料：**

鸟哥私房菜 http://linux.vbird.org/linux_basic/0220filemanager.php
