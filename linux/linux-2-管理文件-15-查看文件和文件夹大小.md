---
author: sam
comments: true
date: 2017-07-12 03:16:22+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-df-du
slug: linux-df-du
title: Linux【2】-管理文件15-查看文件和文件夹大小(df/du)
wordpress_id: 2044
categories:
- linux
tags:
- df
- du
---


当磁盘大小超过标准时会有报警提示，这时如果掌握df和du命令是非常明智的选择。

df可以查看一级文件夹大小、使用比例、档案系统及其挂入点，但对文件却无能为力。
du可以查看文件及文件夹的大小。

两者配合使用，非常有效。比如用df查看哪个一级目录过大，然后用du查看文件夹或文件的大小，如此便可迅速确定症结。

<!-- more -->


## 一、df 报告文件系统磁盘空间的使用情况
	
	[yayug@yayu ~]$ df -h  
	Filesystem            Size  Used Avail Use% Mounted on  
	/dev/sda1             3.9G  300M  3.4G   8% /  
	/dev/sda7             100G  188M   95G   1% /data0  
	/dev/sdb1             133G   80G   47G  64% /data1  
	/dev/sda6             7.8G  218M  7.2G   3% /var  
	/dev/sda5             7.8G  166M  7.2G   3% /tmp  
	/dev/sda3             9.7G  2.5G  6.8G  27% /usr  
	tmpfs                 2.0G     0  2.0G   0% /dev/shm  
	
参数 -h 表示使用「Human-readable」的输出，也就是在档案系统大小使用 GB、MB 等易读的格式。

上面的命令输出的第一个字段（Filesystem）及最后一个字段（Mounted on）分别是档案系统及其挂入点。我们可以看到 /dev/sda1 这个分割区被挂在根目录下。

接下来的四个字段 Size、Used、Avail、及 Use% 分别是该分割区的容量、已使用的大小、剩下的大小、及使用的百分比。 FreeBSD下，当硬盘容量已满时，您可能会看到已使用的百分比超过 100%，因为 FreeBSD 会留一些空间给 root，让 root 在档案系统满时，还是可以写东西到该档案系统中，以进行管理。
	
	df [OPTION]... [FILE]... POSIX 选项: [-kP]
	GNU 选项 (最短方式): [-ahHiklmPv] [-t fstype] [-x fstype] [--block-size=size] [--print-type] [--no-sync] [--sync] [--help] [--version] [--] 
	

描述:

此手册页文档是df的GNU版本. df命令列出指定的每一个文件名所在的文件系统上可用磁盘空间的数量。 如果没有指定文件名,则显示当前所有使用中的文件系统.缺省设置时, 磁盘空间以1K为一块显示,如果环境变量POSIXLY_CORRECT已设 置,则采用512字节为一块显示. 如果参数是一个包含已使用文件系统的磁盘设备名, df命令显示出的是该文件系统的可用空间,而非包含设备结点的文件系 统(只能是根文件系统).此版本的df不能显示未使用文件系统的可用空 间,这是由于大多数系统在响应这样的请求时必须很清楚该文件系统的 结构.

GNU 参数说明
	
	-a, --all
		列出包括BLOCK为0的文件系统 
	--block-size=SIZE use SIZE-byte blocks
		指定块的大小 
	-h,--huma-readable
		用常见的格式显示出大小(例如:1K 234M 2G) 
	-H,--si
		同上,但是这里的1k等于1000字节而不是1024字节 
	-i, --inodes
		用信息索引点代替块表示使用状况 
	-k, --kilobytes
		指定块大小等于1024字节来显示使用状况 
	-l, --local
		只显示本地文件系统使用状况 
	-m, --megabytes
		以指定块大小等于1048576字节(1M)来显示使用状况 
	--no-sync
		在取得使用信息前禁止调用同步 (default) 
	-P, --portability
		使用POSIX格式输出 
	--sync
		在取得使用信息前调用同步 
	-t, --type=TYPE
		只显示指定类型(TYPE)的文件系统 
	-T, --print-type
		输出每个文件系统的类型 
	-x, --exclude-type=TYPE
		只显示指定类型(TYPE)之外的文件系统. 
	-v (忽略)
	--help
		输出该命令的帮助信息并退出 
	--version
		输出版本信息并退出 
		
	[root@localhost ~]# df     #列出各文件系统的磁盘空间使用情况  
	[root@localhost ~]# df -ia    #列出各文件系统的i节点使用情况  
	[root@localhost ~]# df -T    #列出文件系统的类型  
	[root@localhost ~]# df -h    #目前磁盘空间和使用情况 以更易读的方式显示 
	[root@localhost ~]# df -k    #以单位显示磁盘的使用情况  
	
## 二、du 查询文件或文件夹的磁盘使用空间

如果当前目录下文件和文件夹很多，使用不带参数du的命令，可以循环列出所有文件和文件夹所使用的空间。这对查看究竟是那个地方过大是不利的，所以得指定 深入目录的层数，参数：--max-depth=，这是个极为有用的参数！如下，注意使用“*”，可以得到文件的使用空间大小.
提醒：一向命令比linux复杂的FreeBSD，它的du命令指定深入目录的层数却是比linux简化，为 -d。
	
	[root@bsso yayu]# du -h --max-depth=1 work/testing  
	27M     work/testing/logs  
	35M     work/testing  
	  
	[root@bsso yayu]# <span style="color: #ff0000;">du -h --max-depth=1 work/testing/* </span> 
	8.0K    work/testing/func.php  
	27M     work/testing/logs  
	8.1M    work/testing/nohup.out  
	8.0K    work/testing/testing_c.php  
	12K     work/testing/testing_func_reg.php  
	8.0K    work/testing/testing_get.php  
	8.0K    work/testing/testing_g.php  
	8.0K    work/testing/var.php  
	  
	[root@bsso yayu]# du -h --max-depth=1 work/testing/logs/  
	27M     work/testing/logs/  
	  
	[root@bsso yayu]# du -h --max-depth=1 work/testing/logs/*  
	24K     work/testing/logs/errdate.log_show.log  
	8.0K    work/testing/logs/pertime_show.log  
	27M     work/testing/logs/show.log  
	

du 统计文件大小相加

df 统计数据块使用情况

如果有一个进程在打开一个大文件的时候,这个大文件直接被rm 或者mv掉，则du会更新统计数值，df不会更新统计数值,还是认为空间没有释放。直到这个打开大文件的进程被Kill掉。

如此一来在定期删除 /var/spool/clientmqueue下面的文件时，如果没有杀掉其进程，那么空间一直没有释放。
使用下面的命令杀掉进程之后，系统恢复。

fuser -u /var/spool/clientmqueue
	
	-a, --all
		显示对所有文件的统计，而不只是包含子目录。
	-b, --bytes
		输出以字节为单位的大小，替代缺省时1024字节的计数单位。
	--block-size=size
		输出以块为单位的大小，块的大小为 size 字节。( file- utils-4.0 的新选项)
	-c, --total
		在处理完所有参数后给出所有这些参数的总计。这个选项被 用给出指定的一组文件或目录使用的空间的总和。
	-D, --dereference-args
		引用命令行参数的符号连接。但不影响其他的符号连接。 这对找出象 /usr/tmp 这样的目录的磁盘使用量有用， /usr/tmp 等通常是符号连接。 译住：例如在 /var/tmp 下建立一个目录test, 而/usr/tmp 是指向 /var/tmp 的符号连接。du /usr/tmp 返回一项 /usr/tmp , 而 du - D /usr/tmp 返回两项 /usr/tmp，/usr/tmp/test。
	--exclude=pattern
		在递归时，忽略与指定模式相匹配的文件或子目录。模式 可以是任何 Bourne shell 的文件 glob 模式。( file- utils-4.0 的新选项)
	-h, --human-readable
		为每个数附加一个表示大小单位的字母，象用M表示二进制 的兆字节。
	-H, --si
		与 -h 参数起同样的作用，只是使用法定的 SI 单位( 用 1000的幂而不是 1024 的幂，这样 M 代表的就是1000000 而不是 1048576)。(fileutils-4.0 的新选项)
	-k, --kilobytes
		输出以1024字节为计数单位的大小。
	-l, --count-links
		统计所有文件的大小，包括已经被统计过的(作为一个硬连接)。
	-L, --dereference
		引用符号连接(不是显示连接点本身而是连接指向的文件或 目录所使用的磁盘空间)。
	-m, --megabytes
		输出以兆字节的块为计数单位的大小(就是 1,048,576 字节)。
	--max-depth=n
		只输出命令行参数的小于等于第 n 层的目录的总计。 --max-depth=0的作用同于-s选项。(fileutils-4.0的新选项)
	-s, --summarize
		对每个参数只显示总和。
	-S, --separate-dirs
		单独报告每一个目录的大小，不包括子目录的大小。
	-x, --one-file-system
		忽略与被处理的参数不在同一个文件系统的目录。
	-X file, --exclude-from=file
		除了从指定的文件中得到模式之外与 --exclude 一样。 模式以行的形式列出。如果指定的文件是'-',那么从标准输 入中读出模式。(fileutils-4.0 的新选项) GNU 标准选项
	--help
		在标准输出上输出帮助信息后正常退出。
	--version
		在标准输出上输出版本信息后正常退出。
	
## 参考资料：

http://justcoding.iteye.com/blog/2041315
