---
author: sam
comments: true
date: 2015-07-25 07:57:08+00:00
#layout: post
link: http://hugo.qinqianshan.com/environment-variable-changes/
slug: environment-variable-changes
title: Linux【11】-其他4-环境变量的修改
wordpress_id: 398
categories:
- basic_description
tags:
- 环境变量
---

Linux中环境变量包括系统级和用户级，系统级的环境变量是每个登录到系统的用户都要读取的系统变量，而用户级的环境变量则是该用户使用系统时加载的环境变量。
所以管理环境变量的文件也分为系统级和用户级的：

<!-- more -->


## 1.系统级：

（1）/etc/profile：该文件是用户登录时，操作系统定制用户环境时使用的第一个文件，应用于登录到系统的每一个用户。该文件一般是调用/etc/bash.bashrc文件。
/etc/bash.bashrc：系统级的bashrc文件。

（2）/etc/environment:在登录时操作系统使用的第二个文件,系统在读取你自己的profile前,设置环境文件的环境变量。


## 2.用户级（这些文件处于家目录下）：


（1）~/.profile:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。这里是推荐放置个人设置的地方

（2）~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。不推荐放到这儿，因为每开一个shell，这个文件会读取一次，效率肯定有影响。
~/.bash_profile or ~./bash_login:，在登录shell时会读取./bash_profile文件，而不是.profile文件。我认为该文件实现的目的 跟.profile文件是一样的，当查找的资料中需要你修改.bash_profile文件，但却你没有该文件的时候，也可以修改.profile来完成实现。
~/.pam_environment:用户级的环境变量设置文件，没有做测试，不知道管不管用。

## 3.常规用法

查看环境变量

	$PATH
	-bash: /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:

env
可以看到所有的环境变量

1.export PATH=$PATH:/etc/apache/bin

使用这种方法,只对当前会话有效，也就是说每当登出或注销系统以后，PATH 设置就会失效

2.#vi /etc/profile

/etc/profile为系统整体的设置，最好不要修改这个文件

在适当位置添加PATH=$PATH:/etc/apache/bin （注意：＝ 即等号两边不能有任何空格）
这种方法最好,除非你手动强制修改PATH的值,否则将不会被改变
以管理员身份 source /etc/profile

3.#vi ~/.bash_profile

~/.bash_profile或~/.bash_login或~/.profile这个属于用户个人设置，你要改自己的数据，就写入这里。（我的这个是~/.profile或者~/.bashrc）
修改PATH行,把/etc/apache/bin添加进去
这种方法是针对用户起作用的

注意：想改变PATH，必须重新登陆才能生效，以下方法可以简化工作：
如果修改了/etc/profile，那么编辑结束后执行source profile 或 执行点命令 ./profile,PATH的值就会立即生效了。
这个方法的原理就是再执行一次/etc/profile shell脚本，注意如果用sh /etc/profile是不行的，因为sh是在子shell进程中执行的，即使PATH改变了也不会反应到当前环境中，但是source是在当前 shell进程中执行的，所以我们能看到PATH的改变。


## 4.我的配置

	#
	export PATH=/home/qqin/bin:/bioinfo/software/sbin:/bioinfo/software/lib:/bioinfo/software/include:/bioinfo/software/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/zgong/genopipe_py/bin
	export PYTHONPATH=$PYTHONPATH:/home/qqin/lib64/python2.7/site-packages:/home/qqin/lib/python2.7/site-packages:/home/qqin/project/packages
	PS1='${debian_chroot:+($debian_chroot)}\u@\h:[\W]\$'
	
	
	#PERL_MB_OPT="--install_base \"/home/qqin/perl5\""; export PERL_MB_OPT;
	#PERL_MM_OPT="INSTALL_BASE=/home/qqin/perl5"; export PERL_MM_OPT;
	
	export PERLLIB=$PERL5LIB:/home/qqin/perl5/lib
	
	
**参考资料：**

http://lxsym.blog.51cto.com/1364623/392743

http://www.cnblogs.com/mengyan/archive/2012/09/04/2669894.html
