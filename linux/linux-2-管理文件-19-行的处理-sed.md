---
author: sam
comments: true
date: 2017-07-08 07:24:29+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-sed/
slug: linux-sed
title: Linux【2】-管理文件19-行的处理(sed)
wordpress_id: 1005
categories:
- basic_description
tags:
- sed
---

我们先来谈一谈 sed 好了， sed 本身也是一个管线命令，可以分析 standard input 的啦！ 而且 sed 还可以将数据进行取代、删除、新增、撷取特定行等等的功能呢！很不错吧～ 我们先来了解一下 sed 的用法，再来聊他的用途好了！
<!-- more -->

## 一、基本参数

[root@www ~]# sed [-nefr] [动作]
选项与参数：

	
	-n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN
	 的数据一般都会被列出到屏幕上。但如果加上 -n 参数后，则只有经过
	 sed 特殊处理的那一行(或者动作)才会被列出来。
	 -e ：直接在指令列模式上进行 sed 的动作编辑；
	 -f ：直接将 sed 的动作写在一个档案内， -f filename 则可以执行 filename 内的
	 sed 动作；
	 -r ：sed 的动作支持的是延伸型正规表示法的语法。(预设是基础正规表示法语法)
	 -i ：直接修改读取的档案内容，而不是由屏幕输出。


动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作
是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』

function 有底下这些参数：

	
	a ：新增， a 的后面可以接字符串，而这些字符串会在新的一行出现(目前的下一行)～
	 c ：取代， c 的后面可以接字符串，这些字符串可以取代 n1,n2 之间的行！
	 d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
	 i ：插入， i 的后面可以接字符串，而这些字符串会在新的一行出现(目前的上一行)；
	 p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运作～
	 s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配
	 正规表示法！例如 1,20s/old/new/g 就是啦！


## 二、以行为单位的新增/删除功能


sed 光是用看的是看不懂的啦！所以又要来练习了！先来玩玩删除与新增的功能吧！

在行首添加文字
用sed的i\命令在第一行前面插入即可，加上 -i 选项直接操作文件。

	
	sed '1i\Insert this line' file.txt


在文件末尾添加文字

	
	echo "Append this line" >> file.txt


范例一：将 /etc/passwd 的内容列出并且打印行号，同时，请将第 2~5 行删除！
[root@www ~]# nl /etc/passwd | sed '2,5d'
1 root:x:0:0:root:/root:/bin/bash
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....
看到了吧？sed 的动作为 '2,5d' ，那个 d 就是删除！因为 2-5 行给他删除了，所以显示的数据就没有 2-5 行啰～ 另外，注意一下，原本应该是要下达 sed -e 才对，没有 -e 也行啦！同时也要注意的是， sed 后面接的动作，请务必以 '' 两个单引号括住喔！

如果题型变化一下，举例来说，如果只要删除第 2 行，可以使用 nl /etc/passwd | sed '2d' 来达成， 至于若是要删除第 3 到最后一行，则是 nl /etc/passwd | **sed '3,$d'** 的啦，那个钱字号$代表最后一行！

范例二：承上题，在第二行后(亦即是加在第三行)加上『drink tea?』字样！
[root@www ~]# nl /etc/passwd |** sed '2a drink tea'**
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
drink tea
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
嘿嘿！在 a 后面加上的字符串就已将出现在第二行后面啰！那如果是要在第二行前呢？『 nl /etc/passwd |** sed '2i drink tea'** 』就对啦！就是将『 a 』变成『 i 』即可。 增加一行很简单，那如果是要增将两行以上呢？

范例三：在第二行后面加入两行字，例如『Drink tea or .....』与『drink beer?』
[root@www ~]# nl /etc/passwd | sed '2a Drink tea or ......\
> drink beer ?'
1 root:x:0:0:root:/root:/bin/bash
2 bin:x:1:1:bin:/bin:/sbin/nologin
Drink tea or ......
drink beer ?
3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
这个范例的重点是『我们可以新增不只一行喔！可以新增好几行』但是每一行之间都必须要以反斜杠『 \ 』来进行新行的增加喔！所以，上面的例子中，我们可以发现在第一行的最后面就有 \ 存在啦！那是一定要的喔！


## 三、以行为单位的取代与显示功能


刚刚是介绍如何新增与删除，那么如果要整行取代呢？看看底下的范例吧：

范例四：我想将第2-5行的内容取代成为『No 2-5 number』呢？

	
	[root@www ~]# nl /etc/passwd | sed '<span style="color: #ff0000;">2,5c</span> No 2-5 number'
	 1 root:x:0:0:root:/root:/bin/bash
	 No 2-5 number
	 6 sync:x:5:0:sync:/sbin:/bin/sync
	 .....(后面省略).....


透过这个方法我们就能够将数据整行取代了！非常容易吧！sed 还有更好用的东东！我们以前想要列出第 11~20 行， 得要透过『head -n 20 | tail -n 10』之类的方法来处理，很麻烦啦～ sed 则可以简单的直接取出你想要的那几行！是透过行号来捉的喔！看看底下的范例先：

范例五：仅列出 /etc/passwd 档案内的第 5-7 行

	
	[root@www ~]# nl /etc/passwd | <span style="color: #ff0000;">sed -n '5,7p'</span>
	 5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
	 6 sync:x:5:0:sync:/sbin:/bin/sync
	 7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown


上述的指令中有个重要的选项『 -n 』，按照说明文件，这个 -n 代表的是『安静模式』！ 那么为什么要使用安静模式呢？你可以自行下达 sed '5,7p' 就知道了 (5-7 行会重复输出)！ 有没有加上 -n 的参数时，输出的数据可是差很多的喔！你可以透过这个 sed 的以行为单位的显示功能， 就能够将某一个档案内的某些行号捉出来查阅！很棒的功能！不是吗？

pmid = PMID: 24097066,替换为 pmid = "24097066",

	
	<span style="color: #ff0000;">sed -ir 's/PMID: (.*),/"\1",/g' 1701__C_disease.lua    </span>          #-i 替换





## 四、部分数据的搜寻并取代的功能


除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的搜寻并取代的功能喔！ 基本上 sed 的搜寻与取代的与 vi 相当的类似！他有点像这样：

**sed 's/要被取代的字符串/新的字符串/g' 不加这个g的话，就替换一个。**
上表中特殊字体的部分为关键词，请记下来！至于三个斜线分成两栏就是新旧字符串的替换啦！ 我们使用底下这个取得 IP 数据的范例，一段一段的来处理给您瞧瞧，让你了解一下什么是咱们所谓的搜寻并取代吧！

步骤一：先观察原始讯息，利用 /sbin/ifconfig 查询 IP 为何？

	
	[root@www ~]# /sbin/ifconfig eth0
	 eth0 Link encap:Ethernet HWaddr 00:90:CC:A6:34:84
	 inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
	 inet6 addr: fe80::290:ccff:fea6:3484/64 Scope:Link
	 UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
	 .....(以下省略).....


# 因为我们还没有讲到 IP ，这里你先有个概念即可啊！我们的重点在第二行，
# 也就是 192.168.1.100 那一行而已！先利用关键词捉出那一行！

步骤二：利用关键词配合 grep 撷取出关键的一行数据

	
	[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr'
	 inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
	 # 当场仅剩下一行！接下来，我们要将开始到 addr: 通通删除，就是像底下这样：
	 # inet addr:192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
	 # 上面的删除关键在于『 ^.*inet addr: 』啦！正规表示法出现！ ^_^


步骤三：将 IP 前面的部分予以删除

	
	[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | \
	 > <strong>sed 's/^.*addr://g'</strong>
	 192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
	 # 仔细与上个步骤比较一下，前面的部分不见了！接下来则是删除后续的部分，亦即：
	 # 192.168.1.100 Bcast:192.168.1.255 Mask:255.255.255.0
	 # 此时所需的正规表示法为：『 Bcast.*$ 』就是啦！


步骤四：将 IP 后面的部分予以删除

	
	[root@www ~]# /sbin/ifconfig eth0 | grep 'inet addr' | \
	 > sed 's/^.*addr://g' | sed 's/Bcast.*$//g' # <span style="color: #ff0000;">^代表行首，$这里也可以代表行尾</span>
	 192.168.1.100


透过这个范例的练习也建议您依据此一步骤来研究你的指令！就是先观察，然后再一层一层的试做， 如果有做不对的地方，就先予以修改，改完之后测试，成功后再往下继续测试。以鸟哥上面的介绍中， 那一大串指令就做了四个步骤！对吧！ ^_^

让我们再来继续研究 sed 与正规表示法的配合练习！假设我只要 MAN 存在的那几行数据， 但是含有 # 在内的批注我不想要，而且空白行我也不要！此时该如何处理呢？可以透过这几个步骤来实作看看：

步骤一：先使用 grep 将关键词 MAN 所在行取出来

	
	[root@www ~]# cat /etc/man.config | grep 'MAN'
	# when MANPATH contains an empty substring), to find out where the cat
	 # MANBIN pathname
	 # MANPATH manpath_element [corresponding_catdir]
	 # MANPATH_MAP path_element manpath_element
	 # MANBIN /usr/local/bin/man
	 # Every automatically generated MANPATH includes these fields
	 MANPATH /usr/man
	 ....(后面省略)....


步骤二：删除掉批注之后的数据！

	
	[root@www ~]# cat /etc/man.config | grep 'MAN'| sed 's/#.*$//g'
	
	MANPATH /usr/man
	 ....(后面省略)....
	 # 从上面可以看出来，原本批注的数据都变成空白行啦！所以，接下来要删除掉空白行
	
	[root@www ~]# cat /etc/man.config | grep 'MAN'| sed 's/#.*$//g' | \
	 > sed '/^$/d'
	 MANPATH /usr/man
	 MANPATH /usr/share/man
	 MANPATH /usr/local/man
	 ....(后面省略)....
	 直接修改档案内容(危险动作)
	 你以为 sed 只有这样的能耐吗？那可不！ sed 甚至可以直接修改档案的内容呢！而不必使用管线命令或数据流重导向！ 不过，由于这个动作会直接修改到原始的档案，所以请你千万不要随便拿系统配置文件来测试喔！ 我们还是使用你下载的 regular_express.txt 档案来测试看看吧！


范例六：利用 sed 将 regular_express.txt 内每一行结尾若为 . 则换成 !

	
	[root@www ~]# sed -i 's/\.$/\!/g' regular_express.txt
	 # 上头的 -i 选项可以让你的 sed 直接去修改后面接的档案内容而不是由屏幕输出喔！
	 # 这个范例是用在取代！请您自行 cat 该档案去查阅结果啰！


范例七：利用 sed 直接在 regular_express.txt 最后一行加入『# This is a test』
	
	[root@www ~]# sed -i '$a # This is a test' regular_express.txt

#由于 $ 代表的是最后一行，而 a 的动作是新增，因此该档案最后新增啰！
sed 的『 -i 』选项可以直接修改档案内容，这功能非常有帮助！举例来说，如果你有一个 100 万行的档案，你要在第 100 行加某些文字，此时使用 vim 可能会疯掉！因为档案太大了！那怎办？就利用 sed 啊！透过 sed 直接修改/取代的功能，你甚至不需要使用 vim 去修订！很棒吧！

总之，这个 sed 不错用啦！而且很多的 shell script 都会使用到这个指令的功能～ sed 可以帮助系统管理员管理好日常的工作喔！要仔细的学习呢！

注意：在windows中，文本是带有\n\t,而Linux系统带的是\n这两者是有区别的。
要把文件转换一下，有两种方法：
	
	1.命令dos2unix test.file
	2.去掉"\r" ，用命令sed -i 's/\r//' test.file

## 参考资料：

鸟哥私房菜 http://linux.vbird.org/linux_basic/0330regularex.php#sed

专注癌症生物信息研究 http://blog.sina.com.cn/s/blog_4055a594010164yv.html
