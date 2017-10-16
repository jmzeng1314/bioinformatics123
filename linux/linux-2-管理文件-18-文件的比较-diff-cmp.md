---
author: sam
comments: true
date: 2017-07-09 13:52:40+00:00
#layout: post
link: http://hugo.qinqianshan.com/file-diffcmp/
slug: file-diffcmp
title: Linux【2】-管理文件18-文件的比较（diff/cmp）
wordpress_id: 1519
categories:
- basic_description
tags:
- cmp
- diff
---

什么时候会用到档案的比对啊？通常是『同一个软件包的不同版本之间，比较配置文件与原始档的差异』。 很多时候所谓的档案比对，通常是用在 ASCII 纯文本档的比对上的！那么比对档案的指令有哪些？最常见的就是 diff 啰！ 另外，除了 diff 比对之外，我们还可以藉由 cmp 来比对非纯文本档！同时，也能够藉由 diff 建立的分析檔， 以处理补丁 (patch) 功能的档案呢！就来玩玩先！
<!-- more -->

## 一、diff

diff 就是用在比对两个档案之间的差异的，并且是以行为单位来比对的！一般是用在 ASCII 纯文本档的比对上。 由于是以行为比对的单位，因此 diff 通常是用在同一的档案(或软件)的新旧版本差异上！ 举例来说，假如我们要将 /etc/passwd 处理成为一个新的版本，处理方式为： 将第四行删除，第六行则取代成为『no six line』，新的档案放置到 /tmp/test 里面，那么应该怎么做？

	
	[root@www ~]# mkdir -p /tmp/test <==先建立测试用的目录
	[root@www ~]# cd /tmp/test
	[root@www test]# cp /etc/passwd passwd.old
	[root@www test]# cat /etc/passwd | \
	> sed -e '4d' -e '6c no six line' > passwd.new
	# 注意一下， sed 后面如果要接超过两个以上的动作时，每个动作前面得加 -e 才行！
	# 透过这个动作，在 /tmp/test 里面便有新旧的 passwd 档案存在了！
	
接下来讨论一下关于 diff 的用法吧！
	
	[root@www ~]# diff [-bBi] from-file to-file
	选项与参数：
	from-file ：一个档名，作为原始比对档案的档名；
	to-file ：一个档名，作为目的比对档案的档名；
	注意，from-file 或 to-file 可以 - 取代，那个 - 代表『Standard input』之意。
	-i ：忽略大小写的不同。
	-a：将所有文件当作文本文件来处理。
	-b：忽略空格造成的不同。
	-B：忽略空行造成的不同。
	-c：使用纲要输出格式。
	-H：利用试探法加速对大文件的搜索。
	-I：忽略大小写的变化。
	-n --rcs：输出RCS格式。
	


范例一：比对 passwd.old 与 passwd.new 的差异：

	
	
	[root@www test]# diff passwd.old passwd.new
	4d3 <==左边第四行被删除 (d) 掉了，基准是右边的第三行
	< adm:x:3:4:adm:/var/adm:/sbin/nologin <==这边列出左边(<)档案被删除的那一行内容
	6c5 <==左边档案的第六行被取代 (c) 成右边档案的第五行
	< sync:x:5:0:sync:/sbin:/bin/sync <==左边(<)档案第六行内容
	---
	> no six line <==右边(>)档案第五行内容
	# 很聪明吧！用 diff 就把我们刚刚的处理给比对完毕了！
	


用 diff 比对档案真的是很简单喔！不过，你不要用 diff 去比对两个完全不相干的档案，因为比不出个啥咚咚！ 另外， diff 也可以比对整个目录下的差异喔！举例来说，我们想要了解一下不同的开机执行等级 (runlevel) 内容有啥不同？假设你已经知道执行等级 3 与 5 的启动脚本分别放置到 /etc/rc3.d 及 /etc/rc5.d ， 则我们可以将两个目录比对一下：

	
	
	[root@www ~]# diff /etc/rc3.d/ /etc/rc5.d/
	Only in /etc/rc3.d/: K99readahead_later
	Only in /etc/rc5.d/: S96readahead_later
	


我们的 diff 很聪明吧！还可以比对不同目录下的相同文件名的内容，这样真的很方便喔～

## 二、cmp

相对于 diff 的广泛用途， cmp 似乎就用的没有这么多了～ cmp 主要也是在比对两个档案，他主要利用『字节』单位去比对， 因此，当然也可以比对 binary file 啰～(还是要再提醒喔， diff 主要是以『行』为单位比对， cmp 则是以『字节』为单位去比对，这并不相同！)	
	
	[root@www ~]# cmp [-s] file1 file2
	选项与参数：
	-s ：将所有的不同点的字节处都列出来。因为 cmp 预设仅会输出第一个发现的不同点。
	

范例一：用 cmp 比较一下 passwd.old 及 passwd.new	
	
	[root@www test]# cmp passwd.old passwd.new
	passwd.old passwd.new differ: byte 106, line 4
	

看到了吗？第一个发现的不同点在第四行，而且字节数是在第 106 个字节处！这个 cmp 也可以用来比对 binary 啦！

## 参考资料：

鸟哥私房菜 http://linux.vbird.org/linux_basic/0330regularex.php#doc_compare

夕岚一瞥 http://blog.sina.com.cn/s/blog_60f9c0050101akrv.html
