---
author: sam
comments: true
date: 2017-04-20 02:43:01+00:00
#layout: post
link: http://hugo.qinqianshan.com/remote-control-putty-and-teamviewer/
slug: remote-control-putty-and-teamviewer
title: Linux【10】-远程连接2-远程控制--putty和teamviewer,filezila
wordpress_id: 298
categories:
- basic_description
tags:
- putty
- teamviewer
- 远程控制
---

远程控制服务器可以通过putty或teamviewer来实现。首先，我们需要开启服务器的远程控制设置，在“system" "preferences" "remote""desktop"中设置好


# <!-- more -->


方法预览：
1.用PuTTY 和Xming

2.teamviewer

3.  WINSCP 上传下载文件

4. filezila 上传文件


###### 第一种方法：用PuTTY 和Xming 来控制我的服务器 


## 一、Putty

## 1.1putty简介


Using **PuTTY** and** Xming f**or X11 Forwarding The X Window System, or X11, is the window server for Linux. If you’ve used a desktop Linux distribution like Fedora or Ubuntu, you’ve used X11. Desktops like GNOME and KDE are displayed by X11. Programs that you open on a Linux desktop are displayed by X11.
Usually, when you want to connect to your Linux computer from a remote location, you do so with SSH (Secure Shell). SSH lets you make a secure terminal connection to your Linux computer and use the command line. But, wouldn’t it be cool to connect to your remote Linux computer and use its fancy graphical interface? This tutorial will show you how to connect to a remote Linux computer from Windows and use the X11 graphical interface using two free programs, PuTTY and Xming.

PuTTY
PuTTY is a free SSH client that allows you to connect to a remote Linux computer and use the command line. PuTTY can also be used to forward secure data over SSH to other programs. This is called tunneling. We’re going to be taking advantage of PuTTY’s ability to tunnel X11 data. To do that, we first need to install PuTTY.


## 1.2 安装PuTTY


Download **putty.exe** to your Windows desktop from the following address:
http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html（有很多版本，注意选择，x86, putty.exe）
There is no installer. Putty.exe is a self-contained executable program. Put it someplace you can easily find it, and make a desktop shortcut to it if you like. I usually put it in**C:\Program Files**.


## 1.3 PuTTY连接远程linux服务器


When you connect to your remote Linux computer, you will need to set several connection settings to make everything work correctly. PuTTY lets you save these settings in a session so you can reuse them the next time you connect. We’ll make a session that allows PuTTY to forward your Linux computer’s X11 graphical interface over SSH.

1.Open PuTTY on your Windows desktop.

2.PuTTY will open and display the Session panel.
In the Host Name field, type the hostname or IP address of your Linux computer.
In the field underneath the Saved Sessions label, type a name for your saved session.

[![putty_1](/img/2015/06/putty_1.png)](/img/2015/06/putty_1.png)

3.Under the Connection category, expand **SSH** and choose **X11**.
Click the **Enable X11 Forwarding** checkbox.
[![putty_2](/img/2015/06/putty_2.png)](/img/2015/06/putty_2.png)

4. Go back to the Session category and click Save to save your session connection settings.


## 二、Xming


Xming is a free X Window server for the Windows desktop. With Xming, you can display graphical applications from your remote Linux computer on your Windows desktop. To take advantage of Xming’s capabilities, we’ll need to first install it.

**Installing Xming**
1.Download the Xming installer to your Windows desktop from the following address:
http://www.straightrunning.com/XmingNotes/（下载的时候有的打不开，我只好用豆荚翻墙）
2.Look for the Xming link under Public Domain Releases的Xming-mesa-6-9-0-31-setup.exe（因为我的电脑是32位的）.
3. Run the Xming setup program on your Windows desktop. When selecting components to install, make sure the following are selected:
XLaunch Wizard (selected by default)
Normal PuTTY Link SSH Client (selected by default)

**Running Xming**（可以调用图形界面的程序）
The makers of Xming have provided a simple utility called Xlaunch that allows you to configure Xming easily, and also save your configuration for future use. To run Xming, open XLaunch and select the configuration you wish to use. We’ll use the configuration that follows.

1. Open XLaunch from the program menu.
2.Select Multiple Windows and click Next. This tells Xming to open each remote Linux application in a new window.
[![putty_3](/img/2015/06/putty_3.png)](/img/2015/06/putty_3.png)
3.Select Start No Client and click Next. This tells Xming to launch and wait for commands from another program (like PuTTY).

[![putty_4](/img/2015/06/putty_4.png)](/img/2015/06/putty_4.png)
4.Make sure that Clipboard is selected and click Next. This tells Xming to enable your remote Linux applications to share a unified clipboard.

[![putty_5](/img/2015/06/putty_5.png)](/img/2015/06/putty_5.png)
5.Click the Finish button to launch Xming.
[![putty_6](/img/2015/06/putty_6.png)](/img/2015/06/putty_6.png)


## 三、Launching Graphical Applications from your Remote Linux Computer

Now that Xming is running, you can open your PuTTY session and launch a graphical application. My Linux computer is running Fedora 12 with the Gnome desktop. I’m going to launch my remote Linux computer’s calculator on my Windows desktop through the SSH connection using X11 forwarding.

1.Open PuTTY.
2.Double-click on the saved session you created earlier. PuTTY will create an SSH connection to your remote Linux computer.
[![putty_7](/img/2015/06/putty_7.png)](/img/2015/06/putty_7.png)
3.Login to your Linux computer.
4.Type gcalctool at the command prompt and hit enter.
[![putty_8](/img/2015/06/putty_8.png)](/img/2015/06/putty_8.png)
5.If everything worked correctly, your remote Linux computer’s calculator should appear on your desktop. Cool, huh?
[![putty_9](/img/2015/06/putty_9.png)](/img/2015/06/putty_9.png)
If you have any questions, please feel free to ask in the comments section below.

转自http://blog.nth-design.com/2010/05/19/x11-putty-xming/

这样就可以享受远程控制了哦


# 四、Putty实现windows和Linux文件的传输


windows使用的是putty和其附带的pscp（传文件）或者psftp（传文件夹）
http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
选择pscp.exe

打开cmd窗口，输入命令：
pscp 源文件路径 root@hostIp:目的路径
如将aaa.jpg传到/home下，则： pscp aaa.jpg root@192.168.0.101:/home
[![putty_10](/img/2015/06/putty_10.jpg)](/img/2015/06/putty_10.jpg)

[![putty_11](/img/2015/06/putty_11.jpg)](/img/2015/06/putty_11.jpg)

**从linux向windows传文件：**
在windows的cmd中输入 pscp root@hostIp: linux中源文件路径 windows中目的路径
如： pscp root@192.168.0.100:/home/aaa.jpg G:/putty/

注意：使用pscp只能传文件，无法传目录，要传目录的话可以用psftp
成功了！

可以不用root身份而用普通用户身份登陆连接，此时只能将文件传至用户对应的主目录下

参考资料：
http://www.cnblogs.com/ylan2009/archive/2012/01/12/2321126.html


# 五、putty access denied（未解决）


连接后，我发现只有我的sam用户（管理员）可以登录，其他的用户都不行啊

access denied大致有四个原因：

1没有连接正确的服务器
检查你的Ip地址是否正确

2 You're not entering the right username.
你有root权限，但并不意味着你可以以root来登陆，仅仅意味你有root权限（通过sudo）
If you're not entering the username manually, make sure Connection → Data → Auto-login username is actually set to root.

3 密码不正确
Check the password you're entering.
Verify that CapsLock is disabled.
Verify that PuTTY uses the proper keyboard layout.
If you use AltGr, make sure Terminal → Keyboard → AltGr acts as Comopse key isn't checked.
If none of the above works, clear the field Connection → Data → Auto-login username (assuming it is set) and enter the password in the username field to verify it gets entered correctly.

4 Root access is disabled.
Some SSH servers can disable SSH logins for certain users and may even disable root logins by default.
If you're using Open SSH server, you have to edit /etc/ssh/sshd_config and make sure that it doesn't contain one of the following lines:
PermitRootLogin no
PermitRootLogin without-password
Both result in a simple Access denied. error, although they permit you to enter the password. The latter only allows root logins when authenticating using public keys.
To permit root logins, remove the corresponding line or replace it with the following:
PermitRootLogin yes （把No改为yes）
See man sshd_config for further information.
解决方法是简单配置SSH，在/etc/ssh/sshd_config文件中，将PermitRootLoginl默认值“no”改为“yes”，就可以实现使用putty登陆linux（即使用root身份进行登陆）。相反，要禁用root身份登陆，则只要将PermitRootLoginl默认值“yes”改为“no”即可实现！

修改完sshd_config 需要重启ssh.
重新启动sshd
service sshd restart
/etc/init.d/ssh restart
上面的两个方法对我的服务器都不好使

所以必须先 找到sshd的位置，然后重启
sam@sam-Precision-WorkStation-T7500[sam] locate sshd [12:26AM]
/etc/pam.d/sshd
/etc/ssh/sshd_config
/usr/sbin/sshd
/usr/share/doc/openssh-client/examples/sshd_config
/usr/share/man/man5/sshd_config.5.gz
/usr/share/man/man8/sshd.8.gz
/usr/share/vim/vim73/syntax/sshdconfig.vim

sam@sam-Precision-WorkStation-T7500[sam] ps -ef |grep ssh [12:28AM]
root 1041 1 0 Sep23 ? 00:00:00 /usr/sbin/sshd -D
lly 2888 2853 0 Oct20 ? 00:00:00 /usr/bin/ssh-agent /usr/bin/dbus-launch --exit-with-session gnome-session --session=ubuntu
lx 3738 3703 0 Oct20 ? 00:00:00 /usr/bin/ssh-agent /usr/bin/dbus-launch --exit-with-session gnome-session --session=ubuntu
sam 4262 4225 0 Oct20 ? 00:00:00 /usr/bin/ssh-agent /usr/bin/dbus-launch --exit-with-session gnome-session --session=ubuntu
lpf 10179 10144 0 Oct20 ? 00:00:00 /usr/bin/ssh-agent /usr/bin/dbus-launch --exit-with-session gnome-session --session=ubuntu
sam 11062 1 0 Oct20 ? 00:03:52 gedit /etc/ssh/ssh_config
root 22611 1041 0 00:26 ? 00:00:00 sshd: sam [priv]
sam 22776 22611 0 00:26 ? 00:00:00 sshd: sam@pts/1
sam 22805 22777 0 00:33 pts/1 00:00:00 grep --color=auto ssh

/usr/sbin/sshd restart
报错：提示“Extra argument restart.”

kill -HUP $(cat /var/run/sshd.pid)
可以关掉

然后确认sshserver是否启动了：
ps -e |grep ssh
如果看到sshd那说明ssh-server已经启动了。

然后重启SSH服务：
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh start

但是我的还是不好使，只有一个用户能登录，有哥们给我支招：
1，Add greeter-show-manual-login=true to file / etc/ lightdm/ lightdm.conf
2，在sshd_config中加入AllowUsers 用户名
3，将PermitRootLogin的no改为了yes

上面3个方法对我的还是不好使
倒弄了一天了，还是没有解决，先把问题留着吧，待以后来弄

参考资料：
http://bbs.linuxtone.org/thread-5487-1-1.html （sshd_config参数详解）
http://blog.sina.com.cn/s/blog_68431a3b0100zclm.html
http://superuser.com/questions/539139/putty-password-access-denied (超赞)

方法二：teamviewer来控制


# 六、teamviewer


teamviewer(http://www.teamviewer.com/zhCN/download/linux.aspx).介绍见度娘

只要打开软,就会显示你电脑的id和密码，另一方输入就行。感觉QQ远程登录就是山寨人家的这个思路做出来的。

下载的.deb可以在终端输入：

    
    sudo dpkg -i /sam/teamviewer_linux_x64.deb


这个软件抽过几次风，卸载，再按上面的重装。

    
    sudo apt-get remove teamviewer;
    sudo dpkg -i /sam/teamviewer_linux_x64.deb


deb文件的安装：

1、“dpkg -i ”<安装包名称>
安装一个 “Debian ”软件包，如你手动下载的文件。
2、“dpkg -c ”<安装包名称>
列出 <安装包名称> 的内容。
3、“dpkg -I ”<安装包名称>
从 <安装包名称> 中提取包裹信息。
4、“dpkg -r ”<安装包名称>
移除一个已安装的deb包裹。
5、“dpkg -P” <安装包名称>
完全清除一个已安装的包裹。和 “remove” 不同的是，“purge ”另外还删除所有的配制文件。
6、“dpkg -L ”<安装包名称>
列出 <安装包名称> 安装的所有文件清单。同时请看 “dpkg -c ”来检查一个 .deb 文件的内容

## 七、filezila

错误: 读取目录列表失败

解决方法：更改Filezilla设置，编辑-设置-连接-FTP-被动模式，将“使用服务器的外部ip地址来代替”改为“回到主动模式”即可。

ps:

今天多谢徐工远程登录解决了我额文件系统根目录不足的问题。明天我有可以开始我崭新的工作了，这个十一将是多么的有意义的，在实验室中度过，陪着我的将是文献和电脑。

经过一段时间的使用，我发现teamviewer的价值更大哦，又有图形界面，又可以不受局域网的限制。问题在于这种图形界面只能一个人操作。


## 参考资料：

https://www.niwoxuexi.com/blog/linux/article/136.htmlhttp://www.cnblogs.com/niocai/archive/2011/11/02/2233585.html



