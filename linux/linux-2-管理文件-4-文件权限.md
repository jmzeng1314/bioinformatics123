---
author: sam
comments: true
date: 2017-08-31 15:39:00+00:00
#layout: post
link: http://hugo.qinqianshan.com/rights-management-chmod/
slug: rights-management-chmod
title: Linux【2】-管理文件4-权限管理（chmod）
wordpress_id: 419
categories:
- basic_description
tags:
- chmod
- 权限管理
---

# 权限管理

这个问题我在重装系统以后遇到过，就是无法在目录下建立文件夹，也无法复制粘贴，一上网才发现是权限设置的问题。其实在图形界面里面改权限的话，直接在想要操作的文件夹下面**单击右键，选择属性**，然后修改权限就可以，当然，通过命令更会显得自己高端一点。

Linux档案的基本权限就有九个，分别是owner/group/others三种身份各有自己的read/write/execute权限， 先复习一下刚刚上面提到的数据：档案的权限字符为：『-rwxrwxrwx』， 这九个权限是三个三个一组的！

<!-- more -->

其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

    r 100 4
    w 010 2
    x 001 1
    每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为： **[-rwxrwx---] 分数则是：**
    owner = rwx = 4+2+1 = 7
    group = rwx = 4+2+1 = 7
    others= --- = 0+0+0 = 0

一个文件主要包含下列属性，“ls -l”

    - rwx rwx rwx user group date filename

第一个符号 

    - 代表普通文件
    d 代表文件夹
    l 代表链接

111 101 101

其中，第一组为归属用户的权限，第二组为归属群组的权限，第三组为其它用户群组的权限。user为文件的归属用户，group为文件的归属群组，date为日期信息，filename为文件名。
可以使用3位的二进制数字来描述一组权限，某一权限对应的数字为1,则表示具有该种权限，为0,则不具有该种权限。
对于文件夹，必须拥有它的可执行权限，才能够使用“cd”命令进入该文件夹；拥有可读权限，才能够使用“ls”命令查看该文件夹的文件列表。

root用户拥有最高权限。

所以等一下我们设定权限的变更时，该档案的权限数字就是770啦！变更权限的指令chmod的语法是这样的：

    [root@www ~]#  chmod [-R] xyz  档案或目录 
    
    选项与参数：
     xyz : 就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。
     -R : 进行递归(recursive)的持续变更，亦即连同次目录下的所有档案都会变更
     我们还可以使用“a、u、g、o”表示归属关系，使用“=、+、-”表示权限变化，使用“r、w、x”表示权限内容，
     a 所有用户 u 归属用户 g 归属群组 o 其它用户
     = 具有权限 + 增加权限 - 去除权限
     r 可读权限 w 可写权限 x 可执行权限


例如：

    a+x 给所有用户增加可执行权限
    go-wx 将归属群组和其它用户的可写、可执行权限去掉
    u=rwx 归属用户具有可读、可写、可执行权限

chmod <权限表达式> <文件|目录>

改变文件的权限。权限的表达式可以使用三位8进制数字表示，或者使用+-=来表示

    -R 递归
    -v 显示过程
    -c 类似“-v”，仅显示更改部分
    --reference=<参考文件或目录> 以指定文件为参考改变权限


示例：

    chmod -R a+x path
    chmod -Rv 755 path

ps: liunux下的可执行文件并不是由扩展名（例如，exe）决定的，而是由其可执行的权限决定的

**参考资料：**

鸟哥私房菜 http://linux.vbird.org/linux_basic/0210filepermission.php#filepermission_ch
