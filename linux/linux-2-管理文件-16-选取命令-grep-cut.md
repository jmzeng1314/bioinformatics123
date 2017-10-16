---
author: sam
comments: true
date: 2017-07-11 15:13:55+00:00
#layout: post
link: http://hugo.qinqianshan.com/select-the-command-cutgrep/
slug: select-the-command-cutgrep
title: Linux【2】-管理文件16-选取命令(cut/grep)
wordpress_id: 1532
categories:
- basic_description
tags:
- cut
- grep
---

文本处理的过程中，常常需要包含某些字符的行或列的提取额，这里面常用的两个命令cut和grep
<!-- more -->


## 一、cut

cut 不就是『切』吗？没错啦！这个指令可以将一段讯息的某一段给他『切』出来～ 处理的讯息是以『行』为单位喔！底下我们就来谈一谈：

	
	[root@www ~]# cut -d'分隔字符' -f fields <==用于有特定分隔字符
	[root@www ~]# cut -c 字符区间 <==用于排列整齐的讯息
	选项与参数：
	cut 命令逐行地处理输入，并从中取出某些列。这里说的「列」有三种模式：
	-b # 以字节作为标准取出列
	-f ：依据 -d 的分隔字符将一段讯息分割成为数段，用 -f 取出第几段的意思；
	-c ：以字符 (characters) 的单位取出固定字符区间；
	
	-d ：后面接分隔字符。与 -f 一起使用；
	
	cut 命令还支持 --complement 参数，意思是取补集。比如在我们刚才的例子中，取补集就意味着取出第一列、第二列、第六列至第八列以及第十列。
	


范例一：将 PATH 变量取出，我要找出第五个路径。

	
	[root@www ~]# echo $PATH
	/bin:/usr/bin:/sbin:/usr/sbin:/usr/local/bin:/usr/X11R6/bin:/usr/games:
	# 1 | 2 | 3 | 4 | 5 | 6 | 7
	
	[root@www ~]# echo $PATH | cut -d ':' -f 5
	# 如同上面的数字显示，我们是以『 : 』作为分隔，因此会出现 /usr/local/bin
	# 那么如果想要列出第 3 与第 5 呢？，就是这样：
	[root@www ~]# echo $PATH | cut -d ':' -f 3,5
	


范例二：将 export 输出的讯息，取得第 12 字符以后的所有字符串

	
	[root@www ~]# export
	declare -x HISTSIZE="1000"
	declare -x INPUTRC="/etc/inputrc"
	declare -x KDEDIR="/usr"
	declare -x LANG="zh_TW.big5"
	.....(其他省略).....
	# 注意看，每个数据都是排列整齐的输出！如果我们不想要『 declare -x 』时，
	# 就得这么做：
	
	[root@www ~]# export | cut -c 12-
	HISTSIZE="1000"
	INPUTRC="/etc/inputrc"
	KDEDIR="/usr"
	LANG="zh_TW.big5"
	.....(其他省略).....
	# 知道怎么回事了吧？用 -c 可以处理比较具有格式的输出数据！
	# 我们还可以指定某个范围的值，例如第 12-20 的字符，就是 cut -c 12-20 等等！
	


范例三：用 last 将显示的登入者的信息中，仅留下用户大名

	
	[root@www ~]# last
	root pts/1 192.168.201.101 Sat Feb 7 12:35 still logged in
	root pts/1 192.168.201.101 Fri Feb 6 12:13 - 18:46 (06:33)
	root pts/1 192.168.201.254 Thu Feb 5 22:37 - 23:53 (01:16)
	# last 可以输出『账号/终端机/来源/日期时间』的数据，并且是排列整齐的
	
	[root@www ~]# last | cut -d ' ' -f 1
	# 由输出的结果我们可以发现第一个空白分隔的字段代表账号，所以使用如上指令：
	# 但是因为 root pts/1 之间空格有好几个，并非仅有一个，所以，如果要找出
	# pts/1 其实不能以 cut -d ' ' -f 1,2 喔！输出的结果会不是我们想要的。
	<span style="color: #ff0000;">可以用sed 's/ * / /'来将很多空格变为一个
	</span>


也可以这样;

	
	
	好在我们有 tr 命令。使用 -s 参数，可以逐行地将连续的字符 unique 成单独的一个字符。
	\$ who | tr -s ' ' | cut -d ' ' -f 1,3,4
	Liam 2016-11-08 00:07
	Liam 2016-11-08 00:23
	Liam 2016-11-08 00:15
	


但是如果分隔符是tab的话，就只有用awk罗，cut就无能为力了。
cut 主要的用途在于将『同一行里面的数据进行分解！』最常使用在分析一些数据或文字数据的时候！ 这是因为有时候我们会以某些字符当作分割的参数，然后来将数据加以切割，以取得我们所需要的数据。 鸟哥也很常使用这个功能呢！尤其是在分析 log 档案的时候！不过，cut 在处理多空格相连的数据时，可能会比较吃力一点。


## 二、grep


刚刚的 cut 是将一行讯息当中，取出某部分我们想要的，而 grep 则是分析一行讯息， 若当中有我们所需要的信息，就将该行拿出来～简单的语法是这样的：

	
	[root@www ~]# grep [-acinv] [--color=auto] '搜寻字符串' filename
	选项与参数：
	-a ：将 binary 档案以 text 档案的方式搜寻数据
	-c ：计算找到 '搜寻字符串' 的次数
	-i ：忽略大小写的不同，所以大小写视为相同
	-n ：顺便输出行号
	-v ：反向选择，亦即显示出没有 '搜寻字符串' 内容的那一行！
	--color=auto ：可以将找到的关键词部分加上颜色的显示喔！
	


范例一：将 last 当中，有出现 root 的那一行就取出来；

	
	[root@www ~]# last | grep 'root'
	


范例二：与范例一相反，只要没有 root 的就取出！

	
	[root@www ~]# last | grep -v 'root'
	


范例三：在 last 的输出讯息中，只要有 root 就取出，并且仅取第一栏

	
	[root@www ~]# last | grep 'root' |cut -d ' ' -f1
	# 在取出 root 之后，利用上个指令 cut 的处理，就能够仅取得第一栏啰！
	


范例四：取出 /etc/man.config 内含 MANPATH 的那几行

	
	[root@www ~]# grep --color=auto 'MANPATH' /etc/man.config
	....(前面省略)....
	MANPATH_MAP /usr/X11R6/bin /usr/X11R6/man
	MANPATH_MAP /usr/bin/X11 /usr/X11R6/man
	MANPATH_MAP /usr/bin/mh /usr/share/man
	# 神奇的是，如果加上 --color=auto 的选项，找到的关键词部分会用特殊颜色显示喔！
	


范例五：取出包含foo的前几行

	
	grep -C 5 foo file  显示file文件中匹配foo字串那行以及上下5行
	grep -B 5 foo file  显示foo及前5行
	grep -A 5 foo file  显示foo及后5行
	


grep 是个很棒的指令喔！他支持的语法实在是太多了～用在正规表示法里头， 能够处理的数据实在是多的很～不过，我们这里先不谈正规表示法～下一章再来说明～ 您先了解一下， grep 可以解析一行文字，取得关键词，若该行有存在关键词，就会整行列出来！

## 参考资料:

鸟哥linux私房菜cut linux.vbird.org/linux_basic/0320bash.php#cut

http://blog.csdn.net/dztaopao/article/details/9241559

http://liam0205.me/2016/11/07/how-to-remove-a-column-from-a-plain-text-file-on-Linux/
