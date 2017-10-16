---
author: sam
comments: true
date: 2017-07-15 03:06:04+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-download-order/
slug: linux-download-order
title: Linux【2】-管理文件12-2-Shell脚本实现ftp的自动上传下载
wordpress_id: 315
categories:
- basic_description
tags:
- ftp
---

## 一、ftp
自动上传文件

4. ftp自动登录上传单个文件。

	####把本地/home/databachup/a.sh up ftp /home/databackup 下####
	#!/bin/bash
	ftp -n<<!
	open 192.168.1.171
	user guest 123456
	binary
	cd /home/data
	lcd /home/databackup
	prompt
	put a.sh a.sh 
	close
	bye
	!


小结：把以上脚本另存为文件加入到crontab中即可实现ftp自动上传、下载文件。
注解：
1. -n 不受.netrc文件的影响。（ftp默认为读取.netrc文件中的设定）
2. << 是使用即时文件重定向输入。
3. !是即时文件的标志它必须成对出现，以标识即时文件的开始和结尾。


因为ftp只能传文件，不能传文件夹中的文件夹，所以需要借助另外连个工具，
ncftp和Lftp。
我用的是lftp

## 二、ncftp

	brew install ncftp

	ncftpput -R -v -u "ftp-username" ftp.website.com ftp-upload-path local-path/*

## 三、lftp

我用的该方法，但是上次速度有些慢额

	lftp -d -u bizhat,mypassword 174.36.192.112
	set ftp:ssl-allow no
	mirror -R /var/flashwebhost/vshare2.7/ public_html

## 参考资料：

http://www.jb51.net/article/34361.htm
