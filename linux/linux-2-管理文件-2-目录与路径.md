---
author: sam
comments: true
date: 2017-09-04 15:38:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-dir-path/
slug: linux-dir-path
title: Linux【2】-管理文件2-目录与路径（cd/pwd/mkdir/rmdir/path）
wordpress_id: 3099
categories:
- basic_description
---

绝对路径与相对路径：
绝对路径：路径的写法『一定由根目录/ 写起』，例如： /usr/share/doc 这个目录。
相对路径：路径的写法『不是由 / 写起』，例如由/usr/share/doc 要到 /usr/share/man 底下时，可以写成： 『cd ../man』这就是相对路径的写法啦！相对路径意指『相对于目前工作目录的路径！
<!-- more -->

     .         代表此层目录
     ..        代表上一层目录
     -         代表前一个工作目录
     ~         代表『目前用户身份』所在的家目录

~account  代表 account 这个用户的家目录(account是个账号名称)

    cd：变换目录
    pwd：显示当前目录
    mkdir：建立一个新的目录
    rmdir：删除一个空的目录


## 一、cd的用法

    [root@www ~]# cd [相对路径或绝对路径]
    # 最重要的就是目录的绝对路径与相对路径，还有一些特殊目录的符号啰！
    
    [root@www ~]# cd ~vbird
    # 代表去到 vbird 这个用户的家目录，亦即/home/vbird
    
    [root@www vbird]# cd ~
    # 表示回到自己的家目录，亦即是 /root 这个目录
    
    [root@www ~]# cd
    # 没有加上任何路径，也还是代表回到自己家目录的意思喔！
    
    [root@www ~]# cd ..
    # 表示去到目前的上层目录，亦即是 /root 的上层目录的意思；
    
    [root@www /]# cd -
    # 表示回到刚刚的那个目录，也就是 /root 啰～
    
    [root@www ~]# cd /var/spool/mail
    # 这个就是绝对路径的写法！直接指定要去的完整路径名称！
    
    [root@www mail]# cd ../mqueue
    # 这个是相对路径的写法，我们由/var/spool/mail 去到/var/spool/mqueue 就这样写！


## 二、Pwd用法

    [root@www ~]# pwd [-P]
    
    选项与参数：
    -P  ：显示出确实的路径，而非使用链接 (link) 路径。
    
    范例：单纯显示出目前的工作目录：
    
    [root@www ~]# pwd
    /root   <== 显示出目录啦～
    
    范例：显示出实际的工作目录，而非链接文件本身的目录名而已
    
    [root@www ~]# cd /var/mail   <==注意，/var/mail是一个连结档
    
    [root@www mail]# pwd
    /var/mail         <==列出目前的工作目录
    
    [root@www mail]# pwd -P
    /var/spool/mail   <==怎么回事？有没有加 -P 差很多～
    
    [root@www mail]# ls -ld /var/mail
    lrwxrwxrwx 1 root root 10 Sep  4 17:54 /var/mail -> spool/mail
    
    # 看到这里应该知道为啥了吧？因为/var/mail 是连结档，连结到 /var/spool/mail
    # 所以，加上 pwd -P 的选项后，会不以连结文件的数据显示，而是显示正确的完整路径啊！


## 三、mkdir用法：

    [root@www ~]# mkdir [-mp] 目录名称

选项与参数：

    -m ：配置文件案的权限喔！直接设定，不需要看预设权限 (umask) 的脸色～
    -p ：帮助你直接将所需要的目录(包含上层目录)递归建立起来！

范例：请到/tmp底下尝试建立数个新目录看看：

    [root@www ~]# cd /tmp
    [root@www tmp]# mkdir test    <==建立一名为 test 的新目录
    
    [root@www tmp]# mkdir test1/test2/test3/test4
    mkdir: cannot create directory `test1/test2/test3/test4':
    No such file or directory       <== 没办法直接建立此目录啊！
    [root@www tmp]# mkdir -p test1/test2/test3/test4
    # 加了这个 -p 的选项，可以自行帮你建立多层目录！
    
    范例：建立权限为rwx--x--x的目录
    
    [root@www tmp]# mkdir -m 711 test2
    [root@www tmp]# ls -l
    drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
    drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
    drwx--x--x  2 root  root 4096 Jul 18 12:54 test2

仔细看上面的权限部分，如果没有加上 -m 来强制设定属性，系统会使用默认属性。

那么你的默认属性为何？这要透过底下介绍的umask 才能了解喔！


## 四、Rmdir

    [root@www ~]# rmdir [-p] 目录名称

选项与参数：

    -p ：连同上层『空的』目录也一起删除

范例：将于mkdir范例中建立的目录(/tmp底下)删除掉！
    
    [root@www tmp]# ls -l   <==看看有多少目录存在？
    drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
    drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
    drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
    
    [root@www tmp]# rmdir test   <==可直接删除掉，没问题
    [root@www tmp]# rmdir test1  <==因为尚有内容，所以无法删除！
    rmdir: `test1': Directory not empty
    [root@www tmp]# rmdir -p test1/test2/test3/test4
    
    [root@www tmp]# ls -l        <==您看看，底下的输出中test与test1不见了！
    
    drwx--x--x  2 root  root 4096 Jul 18 12:54 test2

瞧！利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 一次删除～

不过要注意的是，这个 rmdir 仅能『删除空的目录』喔！

## 五、Path

范例：先用root的身份列出搜寻的路径为何？

    [root@www ~]# echo $PATH
    /usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin
    :/bin:/usr/sbin:/usr/bin:/root/bin  <==这是同一行！

范例：用vbird的身份列出搜寻的路径为何？

    [root@www ~]# su - vbird
    [vbird@www ~]# echo $PATH
    /usr/kerberos/bin:/usr/local/bin:/bin:/usr/bin:/home/vbird/bin

仔细看，一般用户vbird的PATH中，并不包含任何『sbin』的目录存在喔！

例子：

    [root@www ~]# mv /bin/ls /root
    # mv 为移动，可将档案在不同的目录间进行移动作业
    
    [root@www ~]# /root/ls  <==直接用绝对路径指定该文件名
    [root@www ~]# ./ls      <==因为在 /root 目录下，就用./ls来指定

如果直接输入Ls,及时是在/root目录下也不行，调用程序要么绝对路径，要么相对路径，要么调用环境变量中的，而直接输入程序的名字为调用环境变量中的，环境变量中没有就不可以。

加入环境变量

    [root@www ~]# PATH="$PATH":/root

**参考资料：**

鸟哥私房菜  http://linux.vbird.org/linux_basic/0220filemanager.php
