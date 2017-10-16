---
author: sam
comments: true
date: 2017-06-11 17:19:55+00:00
#layout: post
link: http://hugo.qinqianshan.com/view-the-server-configuration/
slug: view-the-server-configuration
title: Linux【6】-硬盘管理2-查看服务器配置
wordpress_id: 374
categories:
- basic_description
---

现已入职一周，对于我这样的菜鸟来说，呆在这么牛逼的团队里面，压力山大，就连喝水都不敢发声，上厕所都要靠着墙走……废话不多说，开始我的整理吧。这里先来整理一下如何查看自己用的服务器的配置。<!-- more -->

查看服务器配置:(下面的显示结果来自网上)

	root@cyper-pc:/home/cyper# **lshw -short**
	Lshw -

显示硬件信息的列表，这个命令很常用，可以通过不同的选项来显示不同的硬件信息，包括：CPU, 内存，磁盘，USB控制器，硬件驱动器等，该命令主要从/proc目录下的不同文件中获取综合的硬件信息。默认情况下，linux下是不安装这个工具的，所以你需要先安装，然后才能使用，

	wget http://ezix.org/software/files/lshw-B.02.14.tar.gz
	tar -zxvf lshw-B.02.14.tar.gz
	cd lshw-B.02.14
	make &amp;&amp; make install

	1. 查看cpu
	~$ **cat /proc/cpuinfo**
	2. 查看内存占用
	~$ **cat /proc/meminfo**
	3. 硬盘分区
	$ **cat /proc/partitions**
	4. ubuntu 版本
	**cat /proc/version**
	或者
	**cat /proc/version_signature**
	5.ubuntu 版本
	**uname -a**

比如：

	root@cyper-pc:/home/cyper# uname -a
	Linux cyper-pc 3.2.0-35-generic #55-Ubuntu SMP Wed Dec 5 17:42:16 UTC 2012 x86_64 x86_64 x86_64 GNU/Linux
	root@cyper-pc:/home/cyper# cat /proc/version
	Linux version 3.2.0-35-generic (buildd@allspice) (gcc version 4.6.3 (Ubuntu/Linaro 4.6.3-1ubuntu5) ) #55-Ubuntu SMP Wed Dec 5 17:42:16 UTC 2012
	root@cyper-pc:/home/cyper# cat /proc/version_signature
	Ubuntu 3.2.0-35.55-generic 3.2.34

查看ubuntu的版本号 

	cat /etc/issue
	12.04

或者使用System Tools --> System Monitor（图形界面）

/proc目录简介

Linux 内核提供了一种通过 /proc 文件系统，在运行时访问内核内部数据结构、改变内核设置的机制。proc文件系统是一个伪文件系统，它只存在内存当中，而不占用外存空间。它以文件系统的方式为访问系统内核数据的操作提供接口。

用户和应用程序可以通过proc得到系统的信息，并可以改变内核的某些参数。由于系统的信息，如进程，是动态改变的，所以用户或应用程序读取proc文件时，proc文件系统是动态从系统内核读出所需信息并提交的。下面列出的这些文件或子文件夹，并不是都是在你的系统中存在，这取决于你的内核配置和装载的模块。另外，在/proc下还有三个很重要的目录：net，scsi和sys。 Sys目录是可写的，可以通过它来访问或修改内核的参数，而net和scsi则依赖于内核配置。例如，如果系统不支持scsi，则scsi 目录不存在。

## 参考资料：

http://www.2cto.com/os/201309/242272.html

http://www.osetc.com/archives/368.html

http://my.oschina.net/uniquejava/blog/99169

http://blog.csdn.net/zdwzzu2006/article/details/7747977
