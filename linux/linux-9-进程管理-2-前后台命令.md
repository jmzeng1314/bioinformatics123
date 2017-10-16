---
author: sam
comments: true
date: 2017-05-04 07:11:21+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-bg-fg-nohup/
slug: linux-bg-fg-nohups
title: Linux【9】-进程管理2-前后台命令（bg/fg）
wordpress_id: 2621
categories:
- basic_description
tags:
- bg
- fg
- nohup
---

<!-- more -->

Linux下我们如果想一个任务或者程序还后台执行可以使用&，实际上linux还提供了其他任务调度的命令。

## bg

将一个在后台暂停的命令，变成继续执行

## fg

将后台中的命令调至前台继续运行

## jobs

查看当前有多少在后台运行的命令

## ctrl + z

可以将一个正在前台执行的命令放到后台，并且暂停

## nohup 命令

用途：不挂断地运行命令。

	语法：nohup Command [ Arg … ] [　& ]

描述：nohup 命令运行由 Command 参数和任何相关的 Arg 参数指定的命令，忽略所有挂断（SIGHUP）信号。在注销后使用 nohup 命令运行后台中的程序。要运行后台中的 nohup 命令，添加 & （ 表示”and”的符号）到命令的尾部

来看实际操作的例子
启动一个时钟程序：

ctrl+z以后输出为：

这个使用用pidof xclock去查看进程号还是存在，说明程序在后台暂停

执行bg %1 可以看到程序继续执行

一个更明显的例子可以看出bg和fg的区别：firefox &符号有无的区别

## nohup

如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。 该命令的一般形式为：

	nohup conmmand &

如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：

	nohup command > myout.file 2>&1

在上面的例子中，输出被重定向到myout.file文件中


**参考资料：**

http://blog.csdn.net/carolzhang8406/article/details/51314894
