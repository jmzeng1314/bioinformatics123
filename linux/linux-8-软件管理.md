---
author: sam
comments: true
date: 2017-05-21 07:44:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/software-management/
slug: software-management
title: Linux【8】-软件管理
wordpress_id: 1017
categories:
- basic_description
tags:
- yum
---

## 一、RPM管理软件包

RPM 包管理器（简称 RPM，全称为 The RPM Package Manager）是在 Linux下广泛使用的软件包管理器。最早由 Red Hat 研制，现在也由开源社区开发。RPM 通常随附于 Linux 发行版，但也有单独将 RPM 作为应用软件发行的发行版（例如 Gentoo）。

RPM 软件包管理器只能管理以 RPM 形式封装的软件包，并且 RPM 本身也是一个预先安装好的 rpm形式的软件包。对于终端用户而言，RPM 使得软件管理变得十分容易，同时只需要十分简单的命令就能完成 RPM 软件包的安装、卸载和升级。目前是 GNU/Linux 下软件包资源最丰富的软件包类型，也是RHEL 和 CentOS 中默认的软件管理器。

RPM 将要安装的软件先编译过（如果需要的话）并且打包好，通过包装好的软件里预设的数据库记录，记录这个软件要安装的时候必须要的依赖的其它软件，
当安装在 Linux 系统时，RPM 会先根据纪录的数据查询 Linux 系统中依赖的其
它软件是否满足，如果满足则安装，如不满足则不安装。这种安装方式比起用源
代码直接编译进行安装的方式要方便不少。

每个RPM软件包是一个压缩的文档，包含了内容信息，应用程序文件，图标，文档和用作管理的脚本。管理程序利用这些内容来安全地定位、安装和卸载软件。例如，CentOS 安装过程使用随 CentOS 附带的软件包来构建或升级符合您需要的系统。

每个软件包文件都有一个很长的名字，包含了几个最重要的信息。例如，这就是 CentOS 中 tsclient 软件包的全名：

	tsclient-0.132-6.i386.rpm

管理工具处理软件包时，通常使用如下三种格式之一：

* 软件包名称：tsclient
* 带有版本号和发行版本的软件包名称：tsclient-0.132-6
* 带有硬件架构的软件包名称：tsclient.i386

为了清楚明白，rpm 以 “名称.架构” 的格式来列出软件包。仓库通常也将软件包存储在以架构区分的目录中。每次为软件包指定架构的时候，实际指定的是此软件对机器架构的最低要求。

* rpm包有二进制安装包（Binary）以及源代码安装包（Source）两种。二进制包可以直接安装在计算机中，而源代码包将会由 RPM 自动编译、安装。二进制包常常以 rpm 作为后缀名，源代码包经常以 src.rpm作为后缀名。
* rpm 二进制安装包和源代码安装包的安装方式是不一样的。


用法：rpm [选项][安装包名][软件包名]
RHEL 和 CentOS 中自动安装、配制、卸载和升级软件包的工具组合

* -i <安装包文件名> 安装软件
* -v 显示安装详细信息（与 i 连用）
* -h 安装时输出（#）作为进度条（与 iv 连用）
* -e <软件包名> 卸载软件
* --replacepkg 无论软件包是否已被安装，都强行安装
* --test 安装测试，并不实际安装
* --nodeps 忽略软件包的依赖关系强行安装
* --force 忽略软件包及文件的冲突
* -q <软件包名> 查询软件包
* -a 列出所有软件包（与 q 连用）
* -p <安装包文件名> 列出安装后的软件包名（与 q 连用）
	
	#安装
	rpm -ivh 软件包名（下载的软件包） 

	#卸载
	rmp -e opera-12.16-1860.x86_64


在我们的任务中，用 RPM 来管理软件似乎十分方便，事实上 RPM 有一个久为用户诟病的缺点，就是**无法解决软件的依赖问题**。这个问题在我们的任务中
没有体现出来。

简单来说，就是用 RPM 来安装软件时可能会出现以下问题：用户要安装软
件包 A，RPM 可能会提醒需要先安装软件包 B（A 依赖于软件包 B），B 软件包又可能依赖软件包 C，C 软件包又可能依赖于软件包 D …… 有的软件包所依赖
的其他软件包数量甚至多达上百个，安装起来十分麻烦。

究其原因，正是是由于 RPM程序是已经打包好的数据，也就是说，里面的数据已经都编译完成，所有安装时一定需要当初安装时的主机环境才能安装。当初建立这个软件的安装环境必须也要在当前主机上重现才行。


## 二、用yum管理软件包
YUM 是 Yellow dog Updater, Modified 的缩写，是由 Duke University 所发起的计划，目的就是为了解决 RPM 的依赖关系的问题，方便使用者进行软件的安装、升级等等工作。

yum 只是 为了解决 RPM 的依赖关系的问题，而不是一种其它的软件封装模式。RPM 仍然是 RHEL 和 CenOS 中的默认软件管理器。yum 可以视为 RPM 的一个前台工具。


其实 YUM 的工作原理并不复杂，每一个 RPM 软件包的头（header）里面
都会纪录该软件的依赖关系，那么如果可以将该头的内容纪录下来并且进行分析，
可以知道每个软件在安装之前需要额外安装哪些基础软件。也就是说，在服务器
上面先以分析工具将所有的 RPM 软件包进行分析，然后将该分析结果记录下来，
只要在进行安装或升级时先查询该记录文件，就可以知道所有依赖的软件。所以
YUM 的基本工作流程如下：

服务器端：在服务器上面存放了所有的 RPM 软件包，然后以相关的功能去
分析每个 RPM 文件的依赖性关系，将这些数据记录成文件存放在服务器的某特
定目录内。

客户端：如果需要安装某个软件时，先下载服务器上面记录的依赖性关系文
件(可通过 WWW 或 FTP 方式)，通过对服务器端下载的纪录数据进行分析，然
后取得所有相关的软件，一次全部下载下来进行安装。

如果没有安装 yum，就必须去下载，我们可以选择一个最快的 CentOS 官方
源（对于国内用户来说，一般是网易源，我们这里使用的就是是网易源），用下
载工具进行下载，我们需要下载这些包：

	[root@localhost ~]# rpm –qa yum*
	yum-plugin-security-1.1.30-14.el6.noarch # yum 安全插件包
	yum-plugin-fastestmirror-1.1.30-14.el6.noarch# yum 最快镜像自选动择插件包
	yum-metadata-parser-1.1.2-16.el6.x86_64 # yum 的元数据解析器
	yum-utils-1.1.30-14.el6.noarch # yum 操作软件仓库和管理扩展包的工具集合
	yum-3.2.29-30.el6.centos.noarch # yum 软件包
	[root@localhost ~]#

用 wget 命令下载：

	[root@localhost ~]# wget
	http://mirrors.163.com/centos/6.3/os/x86_64/Packages/yum-3.2.29-
	30.el6.centos.noarch.rpm
	[root@localhost ~]# wget http://mirrors.163.com/centos/6.3/os/x86_64/Packages/yummetadata-parser-1.1.2-16.el6.x86_64.rpm

	[root@localhost ~]# wget http://mirrors.163.com/centos/6.3/os/x86_64/Packages/yumutils-1.1.30-14.el6.noarch.rpm
	[root@localhost ~]# wget http://mirrors.163.com/centos/6.3/os/x86_64/Packages/yumplugin-fastestmirror-1.1.30-14.el6.noarch.rpm

在下载完成后，用 rpm 进行安装：

	[root@localhost ~]# rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
	[root@localhost ~]# rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
	[root@localhost ~]# rpm -ivh yum-3.2.29-30.el6.centos.noarch.rpm yum-pluginfastestmirror-1.1.30-14.el6.noarch.rpm
	# 这两个软件包必须同时安装，否则会发生互依赖性问题
	[root@localhost ~]# rpm -ivh yum-utils-1.1.30-14.el6.noarch.rpm

测试 yum

	# yum clean all # 清除 yum 所有软件包缓存信息
	# yum makecache #将服务器上的软件包信息缓存到本地,以提高搜索安装软件的速度
	# yum install finger #安装一个名为“finger”命令，测试 yum 是否可用

当第一次使用 yum 或 yum 源有更新时，yum 会自动下载所有所需的 headers
放置于/var/cache/yum 目录下，所需时间可能较长。

命令 | 含义
------|------
yum clean all  | 清除缓存中旧的 rpm 头文件和包文件
yum makecache  |建立新的缓存
yum list | 列出所有升级源上的包
yum list available  |列出资源库中所有可以安装或更新的 rpm 包
yum list updates  | 列出所有升级源上的可以更新包
yum list installed  |列出已经安装的包
yum search <关键字> | 搜索匹配特定字符的 rpm 包
yum info <软件包名>  |列出资源库中特定的可以安装或更新以及已经安装的 rpm 包的信息
yum install <软件包名> | 安装 rpm 包
yum update <软件包名> |更新 rpm 包
yum remove <软件包名> | 卸载 rpm 包

## 三、yum 软件源

RPM 软件源（Repositories）是一个目录，或是一个网站，包含了许多 RPM
软件包及其索引文件。类似 yum 之类的软件包管理工具可以在仓库中自动地定
位并获取正确的 RPM 软件包。这样，用户就不必手动搜索和安装新应用程序和
升级补丁了。只用一个命令，您就可以更新系统中所有软件，也可以根据指定搜
索目标来查找安装新软件。

有许多公司和社区提供 CentOS rpm 官方源，国内比较常用的有：

	网易源：http://mirrors.163.com
	搜狐源：http://mirrors.sohu.com/
	中科大源：http://centos.ustc.edu.cn/
	CentOS 源：http://mirror.centos.org

我们随时可以到 CentOS 或者 Fedora 的 Public Mirror List 中去查找最快
的 CentOS 或者 Fedora rpm 官方源：

	CentOS:http://www.centos.org/modules/tinycontent/index.php?id=13
	
	Fedora: http://mirrors.fedoraproject.org/publiclist/

一个软件源（Repositories）中可能会配置多个软件仓库(Repository)。
CentOS 默认自带 CentOS-Base.repo 源已经预先配置五个仓库（其中三个默认
启用，两个配置但未启用）：

库名 |内容
------|-----
base |构成 CentOS 基本软件包，和光盘上内容相同，默认启用
updates | Base 仓库中软件包的更新版本，默认启用
extras  |一大批附加的软件包，默认启用
centosplus |针对 base 及 updates 软件库内的组件的更新。这些更新组件并不属于正式的发行，所以在扩展 CentOS 的功能同时牺牲了与先前版本兼容性。启用这个软件库会导致 CentOS 与正式版本有差别。默认情况下是不启用这个库的，用户应该明白这样做的后果才启用 centosplus。
contrib | 这个软件库包含了 CentOS 用户贡献的组件，它们并不会与核心发行版本的组件重叠。这些组件并没有经过 CentOS 的开发者测试，亦未必会同步跟随 CentOS 正式版本的发行。默认情况下是不启用这个库的，用户应该明白这样做的后果才启用contrib。


官方源中去除了很多有版权争议的软件，而且安装的软件也不是最新的稳定
版。CentOS 自带的源中也找不到很多多媒体软件（如 mplayer）和开发软件（如
Eclipse），如果需要安装，必需先添加其他源，如 EPEL、RPMFusion 和
RepoForge 等第三方软件源。

有许多公司和社区为兼容 RHEL 的 Linux 发行版本提供第三方源，比较常用的有：

	EPEL 源：https://fedoraproject.org/wiki/EPEL/zh-cn
	RepoForge 源：http://repoforge.org/
	RPMFusion 源：http://rpmfusion.org/
	Remi 源：http://rpms.famillecollet.com/

	[base]
	#仓库名字
	name=CentOS-$releasever – Base
	#这个可以理解为仓库的描述,这个可以不写这一行
	mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
	#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
	#库的地址
	enable =1
	# 是否启用这个库，1 是启用，0 是不启用，如果 enable 参数缺失，默认为启用
	gpgcheck=1
	#是否检查软件的 KEY，1 是启用，0 是不启用
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	#KEY 文件路径

我们试着去配置一个第三方源，首先确认系统是否安装了 yum-priorities 这个
yum 的插件，这个插件是用来保证安装软件时候软件仓库先后次序，一般是默认
先从官方源安装，然后从第三方软件仓库中安装：

	[root@localhost ~]# yum install yum-priorities
	Loaded plugins: fastestmirror, refresh-packagekit, security
	Loading mirror speeds from cached hostfile
	* base: mirrors.163.com
	* extras: mirrors.163.com
	* updates: mirrors.163.com
	base | 3.7 kB 00:00
	extras | 3.5 kB 00:00
	google-chrome | 951 B 00:00
	updates | 3.5 kB 00:00
	Setting up Install Process
	Resolving Dependencies
	--> Running transaction check
	---> Package yum-plugin-priorities.noarch 0:1.1.30-14.el6 will be installed
	--> Finished Dependency Resolution
	Dependencies Resolved
	===================================================================
	Package Arch Version Repository Size
	===================================================================
	Installing:
	yum-plugin-priorities noarch 1.1.30-14.el6 base 22 k

	Transaction Summary
	===================================================================
	=============
	Install 1 Package(s)
	Total download size: 22 k
	Installed size: 28 k
	Is this ok [y/N]: y
	Downloading Packages:
	yum-plugin-priorities-1.1.30-14.el6.noarch.rpm | 22 kB 00:00
	Running rpm_check_debug
	Running Transaction Test
	Transaction Test Succeeded
	Running Transaction
	 Installing : yum-plugin-priorities-1.1.30-14.el6.noarch 1/1
	 Verifying : yum-plugin-priorities-1.1.30-14.el6.noarch 1/1
	Installed:
	 yum-plugin-priorities.noarch 0:1.1.30-14.el6
	Complete!

安装完以后查看 /etc/yum/pluginconf.d/priorities.conf 文件，确认文件中有
这三行：

	[main]
	enabled=1 #启用本插件
	check_obsoletes=1 #防止一些已经废弃(obsoletes)的包的出现

来到 RepoForge 官方网站 http://repoforge.org/use/ ，根据所使用的 Linux
发行版本选择适合的 rpmforge 发行包，如果不知道你所使用的 CentOS 具体版
本可以通过如下命令查看：

	[root@localhost ~]# cat /etc/centos-release #查看发行版本号
	CentOS release 6.3 (Final)
	[root@localhost ~]# uname –r #查看内核版本号和机器架构
	2.6.32-279.19.1.el6.x86_64
	[root@localhost ~]#

我们可以知道我们所使用的 Linux 发行版本为 CentOS 6.3(Final)，内核版本
为 kernel 2.6.32-279.19.1.el6.x86_64，与 RHEL 6.3 兼容，因此我们选择 EL
6.0 x86_64 版本下载并安装。


	[root@localhost ~]# wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-
	0.5.2-2.el6.rf.x86_64.rpm、
	[root@localhost ~]# rpm -ivh rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
	warning: rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm: Header V3 DSA/SHA1 Signature,
	key ID 6b8d79e6: NOKEY
	Preparing... ########################################### [100%]
	 1:rpmforge-release ########################################### [100%]
	[root@localhost ~]#

	[root@localhost yum.repos.d]# ls -l /etc/yum.repos.d/
	总用量 36
	-rw-r--r--. 1 root root 1926 12 月 26 23:13 CentOS-Base.repo
	-rw-r--r--. 1 root root 637 6 月 26 2012 CentOS-Debuginfo.repo
	-rw-r--r--. 1 root root 626 12 月 26 22:54 CentOS-Media.repo
	-rw-r--r--. 1 root root 2593 12 月 26 22:54 CentOS-Vault.repo
	-rw-r--r--. 1 root root 116 12 月 26 22:54 google-chrome.repo
	-rw-r--r--. 1 root root 739 11 月 13 2010 mirrors-rpmforge
	-rw-r--r--. 1 root root 717 11 月 13 2010 mirrors-rpmforge-extras
	-rw-r--r--. 1 root root 728 11 月 13 2010 mirrors-rpmforge-testing
	-rw-r--r--. 1 root root 1113 11 月 13 2010 rpmforge.repo

用文本编辑器打开/etc/yum.repos.d/rpmforge.repo，找到如下 rpmforge 仓库配
置部分，并在此部分最后添加“priority = 10”，表示该源的优先级较低：

	### Name: RPMforge RPM Repository for RHEL 6 - dag
	### URL: http://rpmforge.net/
	[rpmforge]
	name = RHEL $releasever - RPMforge.net - dag
	baseurl = http://apt.sw.be/redhat/el6/en/$basearch/rpmforge
	mirrorlist = http://apt.sw.be/redhat/el6/en/mirrors-rpmforge
	#mirrorlist = file:///etc/yum.repos.d/mirrors-rpmforge

	enabled = 1
	protect = 0
	gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
	gpgcheck = 1
	priority = 10

优先级由 1 ~ 99 的 99 个数表示，1 的优先级最高。优先级小的源即使有
某软件的较新版本，如果优先级高的源中没有，在启用该插件的情况下，系统也
无法安装/升级到该较新版本。图形界面的 YUM 工具一般默认就已经包含了优
先级插件。

用 vim 打开 CentOS-Base.repo，找到如 base、updates 和 extra 仓库配置部分，
并在分别这些部分最后添加“priority = 1”：

	# CentOS-Base.repo
	#
	# The mirror system uses the connecting IP address of the client and the
	# update status of each mirror to pick mirrors that are updated to and
	# geographically close to the client. You should use this for CentOS updates
	# unless you are manually picking other mirrors.
	#
	# If the mirrorlist= does not work for you, as a fall back you can try the
	# remarked out baseurl= line instead.
	#
	#
	[base]
	name=CentOS-$releasever - Base
	mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
	#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
	gpgcheck=1
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	priority = 1
	#released updates
	[updates]
	name=CentOS-$releasever - Updates
	mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updat
	es
	#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
	gpgcheck=1
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	priority = 1

	#additional packages that may be useful
	[extras]
	name=CentOS-$releasever - Extras
	mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras

	#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
	gpgcheck=1
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	priority = 1

然后来安装mplayer 这个软件来测试RepoForge这个第三方源是否配置好了：

	[root@localhost ~]#yum install mplayer mplayer-gui mplayer-doc mplayer-skins mplayertools

**参考资料：**

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004