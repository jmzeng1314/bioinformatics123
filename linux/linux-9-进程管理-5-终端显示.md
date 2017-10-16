---
author: sam
comments: true
date: 2017-05-01 01:02:26+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-change-the-terminal-shows-a-relative-path/
slug: linux-change-the-terminal-shows-a-relative-path
title: Linux【9】-进程管理5-linux改终端显示相对路径
wordpress_id: 391
categories:
- PS1
tags:
- PS1
---

## 设置

### 1. Linux
打开终端，键入 

	vim ~/.bashrc

找到 
	
	PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
改为 

	PS1='${debian_chroot:+($debian_chroot)}\u@\h:\W\$'

<!-- more -->然后source .bashrc

### 2. mac下
    
    vim /etc/bashrc

将
    #PS1='\h:\W \u\$ '

替换为

    PS1='\u[\W]\$ '

然后source .bashrc


## PS1内容详情

	\a    ASCII 响铃字符（也可以键入 \007）
	\d    "Wed Sep 06" 格式的日期
	\e    ASCII 转义字符（也可以键入 \033）
	\h    主机名的第一部分（如 "mybox"）
	\H    主机的全称（如 "mybox.mydomain.com"）
	\j    在此 shell 中通过按 ^Z 挂起的进程数
	\l    此 shell 的终端设备名（如 "ttyp4"）
	\n    换行符
	\r    回车符
	\s    shell 的名称（如 "bash"）
	\t    24 小时制时间（如 "23:01:01"）
	\T    12 小时制时间（如 "11:01:01"）
	\@    带有 am/pm 的 12 小时制时间
	\u    用户名
	\v    bash 的版本（如 2.04）
	\V    Bash 版本（包括补丁级别）
	\w    当前工作目录（如 "/home/drobbins"）
	\W    当前工作目录的“基名 (basename)”（如 "drobbins"）
	\!    当前命令在历史缓冲区中的位置
	\#    命令编号（只要您键入内容，它就会在每次提示时累加）
	\$    如果您不是超级用户 (root)，则插入一个 "$"；如果您是超级用户，则显示一个 "#"
	\xxx    插入一个用三位数 xxx（用零代替未使用的数字，如 "\007"）表示的 ASCII 字符
	\\    反斜杠
	\[    这个序列应该出现在不移动光标的字符序列（如颜色转义序列）之前。它使 bash 能够正确计算自动换行。
	\]    这个序列应该出现在非打印字符序列之后。


**参考资料：**

http://blog.chinaunix.net/uid-2623904-id-3044238.html

http://www.2cto.com/os/201212/172904.html
