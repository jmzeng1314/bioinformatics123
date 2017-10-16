---
author: sam
comments: true
date: 2017-07-15 03:06:04+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-download-order/
slug: linux-download-order
title: Linux【2】-管理文件12-linux常用下载命令
wordpress_id: 315
categories:
- basic_description
tags:
- Axel
- Curl
- Linuxdown
- MyGet
- Prozilla
- wget
- 下载
---

 > 本文整理了一下Linux下常用的即一个下载命名：wget,Prozilla,MyGet,Linuxdown,Curl,Axel

<!-- more -->

## 一、wget

是一个从网络上自动下载文件的自由工具。它支持HTTP，HTTPS和FTP协议，可以使用HTTP代理.

所谓的自动下载是指，wget可以在用户退出系统的之后在后台执行。这意味这你可以登录系统，启动一个wget下载任务，然后退出系统，wget将在后台执行直到任务完成，相对于其它大部分浏览器在下载大量数据时需要用户一直的参与，这省去了极大的麻烦。

wget可以跟踪HTML页面上的链接依次下载来创建远程服务器的本地版本，完全重建原始站点的目录结构。这又常被称作”递归下载”。在递归下载的时候，wget遵循Robot Exclusion标准(/robots.txt). wget可以在下载的同时，将链接转换成指向本地文件，以方便离线浏览。

wget非常稳定,它在带宽很窄的情况下和不稳定网络中有很强的适应性.如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

### 1.1 wget安装

（由于我的Linux自带，所以就没有自己去安装）
Wget是一个十分常用命令行下载工具，多数Linux发行版本都默认包含这个工具。如果没有安装可在 http://www.gnu.org/software/wget/wget.html  下载最新版本，并使用如下命令编译安装：
    
    #tar zxvf wget-1.9.1.tar.gz
     #cd wget-1.9.1 #./configure
     #make #make install

### 1.2 wget的常见用法:

	wget [选项] [下载地址]

Wget常用参数

	◆-b：后台下载，Wget默认的是把文件下载到当前目录。
	◆-O：将文件下载到指定的目录中。
	◆-P：保存文件之前先创建指定名称的目录。
	◆-t：尝试连接次数，当Wget无法与服务器建立连接时，尝试连接多少次。
	◆-c：断点续传，如果下载中断，那么连接恢复时会从上次断点开始下载。
	◆-r：使用递归下载

除了上述常用功能，Wget还支持HTTP和FTP代理功能，编辑其配置文件“/etc/wgetrc”即可。具体方法是使用VI编辑器打开上述文件，将 “http_proxy”和“ftp_proxoy”前的#去掉，然后在这两项后输入相应的代理服务器的地址，保存退出即可。此外，Wget还可下载整个网站，如下载整个Man手册中心。只需输入如下命令即可： 
	
	wget -r -p -np -k http://man.chinaunix.net

	其中-r参数是指使用递归下载，
	-p是指下载所有显示完整网页所以需要的文件，如图片等，
	-np是指不搜索上层目录，-k则是指将绝对链接转换为相对链接。


## 1.3 常用例子


使用如下的命令下载https链接:

	wget -r -np -nd --accept=gz --no-check-certificate https://www.xxx.com/dir/ --http-user=username --http-password=password
	
	下载'dir'目录下的所有gz文件
	-np 没有父目录
	-nd 不要构建本地目录结构
	--accept=gz 只下载gz文件

1、下载单个文件

	wget url+filename

下载过程中同时可以看到四项信息
已经下载的比例
已经下载的大小
当前下载的速度
剩余的时间

2、使用一个大写O做参数表示另存为
	
	wget -O save_name url

这种方法适用于对应链接中没有显式文件名的情况。
例如：

	wget -O xx.zip http://www.vim.org/scripts/download_script.php?src_id=7701

再用不带-O参数的下载一次。

	ls -al
	总计 132
	drwxr-xr-x 2 root root 4096 07-12 10:43 .
	drwxr-xr-x 4 root root 4096 07-11 16:26 ..
	-rw-r--r-- 1 root root 50243 07-12 10:43 download_script.php?src_id=7701
	-rw-r--r-- 1 root root 50243 07-12 10:43 xx.zip

我们发现，下载的大小都是一样。但是不带-O参数的，文件名还要转换一次。不如用-O参数方便。

	mv "download_script.php?src_id=7701" yy.zip

3、指定下载速率

方法是使用wget --limit-rate
wget程序默认是使用所有的带宽，如果
是在生产服务器上下载很大的文件就不可接受了。
为了避免这种情况使用--limit-rate参数

	wget --limit-rate=200k http://www.openss7.org/repos/tarballs/strx25-0.9.2.1.tar.bz2

4、断点下载

使用wget -c完成未完成的下载
下载到一半需要停下来干别的事情，用^c就可以停顿住。
回来后，继续下载可以加一个-c参数。
注意：如果不加入-c，那么下载的文件会多出一个.1的后缀。

5、在后台下载

方法：加一个-b的参数

	wget -b url/filename

为后台下载。下载经过写入到wget-log文件中。
用tail -f wget-log查看下载日志

6、模拟在浏览器下下载

有的网站不允许客户在非浏览器环境下下载。使用--user-agent来设置
	
	wget --user-agent="Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.3) Gecko/2008092416 Firefox/3.0.3" URL-TO-DOWNLOAD

7、测试下载链接
方法:使用--spider
试图做计划下载时候，需要先检查一下下载链接是否有效。
	
	wget --spider DOWNLOAD-URL

如果返回OK，则表示下载链接是正确的！

例如

	wget --spider "http://ip138.com/ips.asp?ip=58.251.193.137&action=2"
	Spider mode enabled. Check if remote file exists.
	--2010-07-12 11:36:32-- http://ip138.com/ips.asp?ip=58.251.193.137&action=2
	正在解析主机 ip138.com... 221.5.47.136
	Connecting to ip138.com|221.5.47.136|:80... 已连接。
	已发出 HTTP 请求，正在等待回应... 200 OK
	长度：7817 (7.6K) [text/html]
	Remote file exists and could contain further links,
	but recursion is disabled -- not retrieving.

8、增加尝试次数

方法：--tries=1000

如果网速有问题，下载大文件的时候可能会发生错误，
默认wget尝试20次链接。

如果尝试75次，可以

	wget --tires=75 DOWNLOAD-URL

9、下载多个文件使用wget -i

将多个下载链接写入到一个download-file-list.txt文件中，而后用

	wget -i download-file-list.txt

一行一个链接

10、下载整站

方法：用--mirror参数
当你要下载一个完整站点并实现本地浏览的时候，

	wget --mirror -p --convert-links -P ./LOCAL-DIR WEBSITE-URL

参数讲解：

	--mirror：设置这个参数用来建立本地镜像
	-p：下载所有html文件适合显示的元素
	--convert-links：下载完成后，将文档链接都转换成本地的
	-P ./LOCAL-DIR：保存所有的文件和目录到指定文件夹下

11、下载时候禁止下载指定类型的文件

例如下载站点时候，不打算下载gif动画图片。

	wget --reject=gif WEBSITE-TO-BE-DOWNLOADED

12、记录下载日志

方法：使用小写字母o

	wget -o xx.html.log -O xx.html "http://ip138.com/ips.asp?ip=58.251.193.137&action=2"
	检查一下日志：
	[root@localhost opt]# cat xx.html.log
	--2010-07-12 11:57:22-- http://ip138.com/ips.asp?ip=58.251.193.137&action=2
	正在解析主机 ip138.com... 221.5.47.136
	Connecting to ip138.com|221.5.47.136|:80... 已连接。
	已发出 HTTP 请求，正在等待回应... 200 OK
	长度：7817 (7.6K) [text/html]
	Saving to: `xx.html'
	0K ....... 100% 65.5K=0.1s
	2010-07-12 11:57:22 (65.5 KB/s) - `xx.html' saved [7817/7817]

13、是第9条的增强版。可以限制下载容量

	wget -Q5m -i FILE-WHICH-HAS-URLS

当下载的文件达到5兆的时候，停止下载。
注意：如果不是对一个文件下载链接清单，对单个文件，
这个限制不会生效的。

14、和第11条正好相反

这条技巧是讲述如何仅仅下载指定类型的文件
从一个网站中下载所有的pdf文件

	wget -r -A.pdf http://url-to-webpage-with-pdfs/

15、使用wget完成ftp下载

匿名ftp下载类似于http下载

	wget ftp-url即可。

如果是需要输入用户名和密码，则是

	wget --ftp-user=USERNAME --ftp-password=PASSWORD DOWNLOAD-URL

用wget做站点镜像:

	wget -r -p -np -k http://dsec.pku.edu.cn/~usr_name/

或者

	wget -m http://www.tldp.org/LDP/abs/html/

在不稳定的网络上下载一个部分下载的文件，以及在空闲时段下载

	wget -t 0 -w 31 -c http://dsec.pku.edu.cn/BBC.avi -o down.log &

或者从filelist读入要下载的文件列表

	wget -t 0 -w 31 -c -B ftp://dsec.pku.edu.cn/linuxsoft -i filelist.txt -o down.log &

上面的代码还可以用来在网络比较空闲的时段进行下载。我的用法是:在mozilla中将不方便当时下载的URL链接拷贝到内存中然后粘贴到文件filelist.txt中，在晚上要出去系统前执行上面代码的第二条。

使用代理下载

	wget -Y on -p -k https://sourceforge.net/projects/wvware/

代理可以在环境变量或wgetrc文件中设定

在环境变量中设定代理

	export PROXY=http://211.90.168.94:8080/

在~/.wgetrc中设定代理
	
	http_proxy = http://proxy.yoyodyne.com:18023/
	ftp_proxy = http://proxy.yoyodyne.com:18023/

wget各种选项分类列表

	* 启动
	-V, –version 显示wget的版本后退出
	-h, –help 打印语法帮助
	-b, –background 启动后转入后台执行
	-e, –execute=COMMAND 执行`.wgetrc’格式的命令，wgetrc格式参见/etc/wgetrc或~/.wgetrc

	* 记录和输入文件
	-o, –output-file=FILE 把记录写到FILE文件中
	-a, –append-output=FILE 把记录追加到FILE文件中
	-d, –debug 打印调试输出
	-q, –quiet 安静模式(没有输出)


## 二、Prozilla

Prozilla也是一个十分流行的命令行下载工具，支持多线程下载和断点续传功能。可到 http://prozilla.genesys.ro/ 
下载最新的1.3.7.4安装包，下载安装包后使用如下命令进行安装：

	#tar zxvf prozilla-1.3.7.4.tar.gz
	#cd prozilla-1.3.7.4
	#./configure #make
	#make install

	Prozilla命令格式如下： #proz [参数] [下载地址] 常用的选项有：
	◆-k=n ：设置n个线程下载。不加此参数指定线程数，Prozilla默认为4线程下载。
	◆-P, --directory-prefix=DIR：指定将下载的文件保存在DIR/目录。
	◆-r, --resume：继续下载未完成的文件。如果要指定线程数下载可用如下命令： #proz -k=5 http://64.12.204.21/pub/mozilla.org/firefox/releases/1.0/linux-i686/zh-CN/firefox-1.0.installer.tar.gz 这样便以5线程进行文件的下载，并将文件保存到当前目录。

和Wget一样，Prozilla也提供了续传功能，下载中断后，重新输入上述命令，就会出现提示续传，按R键就可继续下载了。


## 三、MyGet

MyGet目标设计成一个可扩展的，拥有丰富界面的多线程下载工具，它支持HTTP、FTP、HTTPS、MMS、RTSP等协议。在 http://myget.sourceforge.net/release/myget-0.1.0.tar.bz2 下载其最新版本0.1.0，下载后使用如下命令安装：

	#tar jxvf myget-0.1.0.tar.bz2
	#cd myget-0.1.0 #./configure
	#make
	#make install

MyGet命令格式如下： #mytget [选项] [下载地址] 常用的选项：
	
	◆-d [目录]：指定下载到的文件在本地存放的位置，默认当前目录。
	◆-f [文件]：指定下载文件名称。
	◆-h：帮助选项。
	◆-n [线程数]：下载线程数量，默认为4个。
	◆-x [代理服务器地址]：设置代理服务器地址，如“-x http://user:password@host:port”。 

MyGet常用的形式如下：
	
	#mytget －d /root/ -n 10 http://lumaqq.linuxsir.org/download/patch/lumaqq_2004t_patch_2005.07.21.00.00.zip

## 四、Linuxdown

Linuxdown是一个命令行多线程下载工具，最多可支持30线程的下载。在 https://gro.clinux.org/frs/download.php/1015/linuxdown-1.0.0.tar.gz 下载最新的1.1.0版本。然后使用如下命令进行编译安装：

	#tar zxvf linuxdown-1.1.0.tar.gz
	#cd dandelion/
	#make
	#make install

Linuxdown格式为： #linuxdown [下载地址] [选项] [线程数] 需要注意的是下载地址和选项都需要西文引号括起来，线程数不可超过30个。一个典型的下载如下：
	
	#linuxdown "http://lumaqq.linuxsir.org/download/patch/lumaqq_2004t_patch_2005.07.21.00.00.zip" 30

# 五、Curl

Curl也是Linux下不错的命令行下载工具，小巧、高速，唯一的缺点是不支持多线程下载。在http://curl.haxx.se/download/curl-7.14.0.tar.gz

下载最新版本。下载后便可使用如下命令编译安装：

	#tar zxvf curl-7.14.0.tar.gz
	#cd curl-7.14.0/
	#./configure
	#make
	#make test
	#make install

Curl使用格式如下： #curl [选项][下载地址] Curl典型下载如下： 

	#curl -O http://10.1.27.10/~kennycx/tools/lumaqq_2004-linux_gtk2_x86_with_jre.tar.gz 

使用Curl下载一个文件并保存到当前目录。此外，Curl虽然不支持多线程下载，但它可同时下载多个文件或下载文件的某一部分，可使用如下命令实现：

	#curl -r 0-199 http://www.netscape.com/ 

获得文件的前200bytes。对于常用的代理下载Curl也可轻松实现，具体操作如下： 

	#curl -x 10.1.27.10:1022 ftp://ftp.funet.fi/README 

使用代理地址为10.1.27.10端口为1022的代理服务器下载一个文件。 

	#curl -U user:passwd -x 10.1.27.10:1022 ftp://ftp.funet.fi/README 

如果代理服务器需要特别的验证，则需要在user:passwd处输入合法的帐号和密码。

**wget与curl的区别**

* wget是个专职的下载利器，简单，专一，极致；而curl可以下载，但是长项不在于下载，而在于模拟提交web数据，POST/GET请求，调试网页，等等。
* 在下载上，也各有所长，wget可以递归，支持断点；而curl支持URL中加入变量，因此可以批量下载。
* 个人用途上，我经常用wget来下载文件，加 -c选项不怕断网；使用curl 来跟网站的API 交互，简便清晰。

## 六、Axel

Axel是命令行下的多线程下载工具，支持断点续传，速度通常情况下是Wget的几倍。可在 http://www.linuxfans.org/nuke/modules.php?name=Site_Downloads&op=mydown&did=1697 下载。下载后使用如下命令编译安装：

	#tar zxvf axel-1.0a.tar.gz
	#cd axel-1.0a/
	#./configure
	#make
	#make install

基本的用法如下： #axel [选项] [下载目录] [下载地址] 一个典型下载如下：

	#alex -n 10 -o /home/kennycx/ http://10.1.27.10/~kennycx/tools/lumaqq_2004-linux_gtk2_x86_with_jre.tar.gz 

用10线程将指定路径的文件下载到/home/kennycx/这个目录下。


**参考资料：**

http://blog.csdn.net/tianxw1209/article/details/6343129

http://www.itqun.net/content-detail/511328.html

http://www.guanwei.org/post/LINUXnotes/05/Linux-Wget-download-method.html

http://www.guanwei.org/post/LINUXnotes/05/Linux-Wget-download-method.html

http://www.zhihu.com/question/19598302

http://www.cnblogs.com/inteliot/archive/2012/06/01/2530843.html
