---
author: sam
comments: true
date: 2017-04-21 15:50:02+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-ssh-connection/
slug: linux-ssh-connection
title: Linux【10】-远程连接-ssh
wordpress_id: 622
categories:
- basic_description
tags:
- ssh
---

## 一、安装软件包

事实上，在我们使用的 CentOS 6 当中，SSH 所需要的所有软件包默认就
已经安装好了。其中包含了 openssh(核心文件)、openssh-clients（SSH 客户端）
和 openssh-server（SSH 服务器端）。我们可以用 yum 命令进行查看来确认这
些软件包都已经被安装好了。

    [root@localhost ~]# yum list installed openssh*

如果没有安装，则

    [root@localhost ~]# yum install openssh

## 二、启动服务

openssh-server 的守护程序名为 sshd。sshd 在系统启动时由启动脚本
（/etc/init.d/sshd）自动启动。我们可以用“chkconfig --list” 命令列出系统服务
列表，找到 sshd，一般来说其状态应该如下所示：
    
    查看sshd的状态
    service sshd status

    [root@localhost ~]# chkconfig --list|grep sshd
    sshd 0:关闭 1:关闭 2:启用 3:启用 4:启用 5:启用 6:关闭
    # 表示 sshd 在运行级别 2、3、4 和 5 都是开机自动启动的（运行级别的解释可参考错误!
    未找到引用源。）
    [root@localhost ~]#

当然，如果你的系统上 ssh 服务没有开机自动启动的，那么只要简单用如下
chkconfig 命令让其在相应运行级别开机自启动就可以了。

    [root@localhost ~]# chkconfig - -level 2345 sshd on

也可以使用 service 命令或者使用/etc/init.d/sshd 脚本手动启动/停止/重启
SSH 服务等操作。

## 三. 开放端口

最后，我们需要确认系统防火墙(iptables)是否开放了 SSH 服务的 22 端口。第 3 行所示，那么就表示 SSH 服务的 22 端口正常开放，否则就需要用 vi
打开 iptables 的配置文件/etc/sysconfig/iptables 添加该行，然后重启防火墙服务
即可。

当然也可以直接使用 CentOS 中提供的伪图形界面防火墙设置工具
system-config-firewall 来设置。

    [root@localhost ~]# cat /etc/sysconfig/iptables|grep 22
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
    [root@localhost ~]#

至此，SSH 服务器就架设好了，可以接受客户端远程联机请求了。

## 四. 安全配置

但此时 SSH 服务器并不安全，我们还需要进行一些额外安全配置，使得 SSH
服务器变得更加安全靠谱。

SSH 协议确实是一个安全的协议，但此“安全”指的是 SSH 协议传输
的数据是经过强度较高的加密算法加密的，因此“安全”，这并不等于
SSH 服务本身就是一个安全的服务，一个没有经过良好配置的 SSH
服务器仍是黑客下手的好对象。

安全配置可以分三步走：（1）SSH 服务器自身安全配置；（2）系统防火墙
（tcp_wrappers1）配置；（3）通过可靠的手段向用户公布主机指纹。

首先，是通过对 SSH 服务器本身进行配置来提高其安全级别，openssh 的
主要配置文件为和/etc/ssh/sshd_config。OpenSSH 默认
从此文件读取配置信息。用 vim 打开/etc/ssh/sshd_config，我们主要将改动如下
几个配置，通过改动这些配置，我们可以拒绝特定用户联机，并且主动断开长时
间没有动作的客户端：

    [root@localhost ~]# vim /etc/ssh/sshd_config
    
    #表示不接受 root 用户远程联机
    PermitRootLogin no
    
    #表示不接受口令为空的用户远程联机
    PermitEmptyPasswords no
    
    #表示不接受主组为“not_ssh_user”用户远程联机
    DenyGroups not_ssh_user
    
    #表示不接受名为“dev”用户远程联机
    DenyUsers dev
    
    #表示当客户端 30 秒内没有任何动作，服务器将发送一个 alive 消息给客户端，如果连发 4
    #次 alive 消息，客户端仍然没有动作，服务器将断开连接.
    ClientAliveInterval 30
    ClientAliveCountMax 4

* 作为管理员，可以考虑将不允许通过 SSH 远程联机的用户归到一个用户组中，然后拒绝这个组远程联机；
* 同时为了安全起见，可以考虑不允许 root 远程联机，在需要用 root身份进行管理时，通过普通用户远程登录到主机，然后再用“su –root”命令获得 root 权限。

接着，可以用 tcp_wrappers 来指定可以访问 ssh 服务的特定 IP 地址，同时
拒绝其他 IP 地址访问 ssh 服务

首先用 vim 打开/etc/hosts.deny 文件，添加如下行，表示拒绝所有 ssh 连接：

    sshd:ALL:deny

/etc/hosts.deny 文件就定义完成了。接下来，打开/etc/hosts.allow 文件，添
加如下行，表示只接受来自 192.168.71.0 网段和地址为 39.187.121.53 主机的
连接。

    sshd: 39.187.121.53 192.168.71.0/255.255.255.0:allow

在这里，我们只想让拥有内网 IP（192.168.71.0）和指定的外网 IP
（39.187.121.53）的远程终端通过 SSH 登录到服务器，以最大限度减少不安全
因素。

* 在任何情况下都不能将 SSH 登录权限开放给网络上的所有主机，尤
其是 Internet 上的主机。

最后，管理员可以通过安全渠道（可靠论坛、BBS、电邮或者电话）向终端
用户公布本主机的 SSH 指纹，用户可以比对此公开指纹和登录主机的指纹来确
认主机身份的真实性，以防止遭受中间人（man-in-the-middle）攻击（在任务 2
中会涉及到主机指纹的比对）。

    [root@localhost ~]# ssh-keygen -lf /etc/ssh/ssh_host_rsa_key
    # 用主机私钥生成主机密钥指纹，注意只有 root 才有读写主机私钥的权限
    2048 42:d6:82:81:5d:20:2e:d3:c8:9b:09:e4:91:c1:6f:f4 /etc/ssh/ssh_host_rsa_key.pub
    (RSA)
    # 42:d6:82:81:5d:20:2e:d3:c8:9b:09:e4:91:c1:6f:f4 就是主机密钥指纹


* 中间人攻击（Man-in-the-Middle Attack, MITM）是一种由来已久的网
络入侵手段，并且在今天仍然非常见，如 ARP 欺骗、DNS 欺骗等都
是典型的 MITM。简而言之，所谓的 MITM 就是通过拦截通信的双方
正常的网络通信数据，并进行窃取和篡改，而通信的双方却毫不知情。
* MITM 一般有两种模式：一是当主机 A 和 B 通信时，C 可以拦截 A
的通信数据，假冒 B 与 A 进行通信来窃取 A 的敏感信息，当下常见
的“钓鱼网站”就属于此类 MITM；二是当主机 A 和 B 通信时，被主
机 C 拦截并为其“转发”，而 A、B 之间并没有真正意思上的直接通
信，他们之间的信息传递是通过 C 作为中介来完成的，但是 A、B 没
有意识到，而以为它们之间是在直接通信。这样攻击主机在中间成为
了一个转发器，C 不仅可以窃取 A、B 的敏感信息还可以将这些信息
进行恶意篡改后再传给对方。


## 五、在 Linux 上用 SSH 客户端远程登录主机

[ssh](http://www.aiezu.com/tag/ssh)登录提供两种认证方式：口令(密码)认证方式和密钥认证方式。

**方法一** 

在 Linux 终端上通过 SSH 登录客户端非常简洁，使用的是 openssh-clients
（SSH 客户端）这个软件包，使用的命令是 ssh。接下来我们就通过 ssh 来登录
到我们刚刚建立好 SSH 服务器的主机上去。

    [root@localhost ~]# ssh stu@192.168.71.128
    # 连接到自己本机上面的 ssh 服务
    The authenticity of host '192.168.71.128 (192.168.71.128)' can't be established.
    # 首次连接主机时会提醒用户确认主机的身份
    RSA key fingerprint is 42:d6:82:81:5d:20:2e:d3:c8:9b:09:e4:91:c1:6f:f4.
    #通过主机 RSA 公钥生成的主机指纹
    Are you sure you want to continue connecting (yes/no)? yes
    # 填入完整的 yes(而不是 y)来同意连接主机
    Warning: Permanently added '192.168.71.128' (RSA) to the list of known hosts.
    #记录下主机的公钥，以便于下次连接时进行指纹比对
    stu@192.168.71.128's password: #输入用户口令
    Last login: Fri Feb 8 16:42:44 2013 from localhost
    [stu@localhost ~]$ exit #如果离开终端，要及时登出
    logout
    Connection to 192.168.71.128 closed.
    [root@localhost ~]#

你可能注意到了在登录过程中，在输入用户口令之前，会出现了一个奇怪的
“ The authenticity of host '192.168.71.128 (192.168.71.128)' can't be
established.（主机 192.168.71.128 的真实性无法确实）”的提示，并且在后面
还给出了一个所谓的“RSA key fingerprint（RSA 密钥指纹）”，你只有输入了
“yes”确认要登录此主机后才能继续输入用户口令。

* 事实上，这是一种中间人攻击防护措施。SSH 客户端在首次远程连
接 SSH 服务器的时候，客户端将获取服务器上的主机公钥并保存在
客户端相关文件中，并通过该主机公钥生成一个主机指纹（一串字符）
提示给用户。
* 此时如果用户知道主机私钥生成的主机指纹（由管理员事先告知或者
自行通过本地登录主机获取），那么你就可以比对这两个指纹是否相
同，如果相同，那么这台主机的身份就是真实无误的，可以键入“yes”
放心登录。如果不相同，那么就要怀疑有其他不怀好意的主机冒充我
们要登录的主机，诱骗我们登录来窃取信息了。此时我们就需要向主
机管理员进行确认后再行登录了。

当然并非每次登录都需要进行主机指纹确认，当客户端成功连接过这台主机
一次后，如果主机指纹此后没有发生变化，那么就不会再给出确认主机指纹的提
示了。

如果你曾经登录过这台主机，但是又让你确认主机指纹，那么就
表示主机指纹发生了变化，可能由以下 3 种情况所导致的：

* A. 主机重新安装了操作系统或者重新安装了 openssh 服务
器；
* B. 这台主机可能有多个 IP 地址，这次登录是另外一个 IP 地
址；
* C. 有其他不怀好意的主机冒充我们要登录的主机，诱骗我们
登录来窃取主机口令。

一般来说，我们碰到的都应该是前两个情况，输入“yes”就行
了，但如果要登录的主机确实很敏感，建议输入“no”，向主机管理员进行确认后再行登录。

**方法二：**

这里介绍密钥认证方式登录到[linux](http://www.aiezu.com/category/system/linux)/[unix](http://www.aiezu.com/category/system/unix)的方法。<!-- more -->

为了提高主机的安全级别，OpenSSH 可以禁止用户以密码身份认证方式登录，
而基于密钥身份认证的方式登录。

1. 禁止用户以密码身份认证方式登录

首先修改 SSH 的配置文件禁止用户以密码身份认证方式登录。打开
/etc/ssh/sshd_config 文件。进行修改，并保存退出

    [root@localhost ~]# vim /etc/ssh/sshd_config
    #PasswordAuthentication yes #找到这一行，将 yes 改为 no
    PasswordAuthentication no #修改后变为此状态，不允许密码方式的登录

在修改完 SSH 的配置文件后，需要重新启动 SSH 服务才能使新的设置生效。

    [root@localhost ~]# /etc/rc.d/init.d/sshd restart #重新启动 SSH 服务
    Stopping sshd: [ OK ]
    Starting sshd: [ OK ]

这时，在远程终端上用 SSH 客户端软件以密码验证身份的的方式就无法登
录主机了。如果用 Linux SSH 客户端登录，会给出“Permission denied”的提示。

    [root@localhost ~]# ssh stu@192.168.71.128
    Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
    [root@localhost ~]#

使用密钥登录分为3步：

目标：A电脑连接B服务器
 	
  1. **A电脑生成密钥（公钥与私钥）；**
  2. **放置A电脑的公钥(Public Key)到服务器B~/.ssh/authorized_key文件中；**
  3. **配置ssh客户端使用密钥登录。**


1、在Linux远程服务器生成密钥：
    
    cd ~
    ssh-keygen -t rsa -C "youremail@example.com"   # 建立公钥与私钥
     
    Generating public/private rsa key pair.
     Enter file in which to save the key (/home/kaz/.ssh/id_rsa): 　← 钥匙的文件名，这里保持默认直接回车
     Created directory '/home/kaz/.ssh'
     Enter passphrase (empty for no passphrase): 　← 输入口令
     Enter same passphrase again: 　 ← 再次输入口令
     Your identification has been saved in /home/kaz/.ssh/id_rsa.
     Your public key has been saved in /home/kaz/.ssh/id_rsa.pub.
     The key fingerprint is:
     tf:rs:e3:7s:28:59:5s:93:fe:33:84:01:cj:65:3b:8e centosaline @localhost . centosaline .com

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

* 作为系统管理员，为了防止别人知道主机用户的密钥，你需要定期地重新生成并分发密钥；
* 作为系统管理员，需要确保用户授权的密钥文件(.ssh/authorized_keys) 读写权限为 400。

2.放置公钥(Public Key)到服务器~/.ssh/authorized_key文件中
    
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys 　 # 公钥内容输出到相应文件中

服务器中的authorized_keys可以存放多个Id_rsa，我就有两台电脑连接到服务器上

3.配置ssh客户端使用密钥登录

然后，将私钥通过安全的方式转移到欲通过SSH连接到服务器的PC上。我通过xshel来链接。当然，你也可以用xshell来生成密匙和公匙，然后将公匙放到~/.ssh/authorized_keys。
    
    ssh -p ** qqin@58.**.***.** -i id_rsa

## 六、在 windows 上用 SSH 客户端远程登录主机
如果使用 Windows 系统，Windows 下默认没有安装支持 SSH 协议的客户
端软件。用户需要自行下载安装。我们推荐使用 PuTTY，这是一款开源的 SSH
客户端软件，能够在多种平台上运行（当然包括 Windows 目前流行的各个版本）。
可以在如下地址下载到 PuTTY:

    http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

打开这个下载页面，我们注意到列出的 PuTTY 相关下载文件总共有 7 个，
这 7 个文件均是可执行文件，分别有不同的用途，其中有 2 个文件是我们必须下
载的：

文件名 | 用途
-----|------
PuTTY.exe  | 远程联机客户端,使用 Telnet 和 SSH 协议（必须）
PSCP.exe SCP |客户端, 命令行下通过 SSH 拷贝文件，类似于Unix/Linux 下的 scp 命令（可选）
PSFTP.exe  | SFTP 的命令行客户端，类似于 FTP 的文件传输，只不过使用的是 SSH 的 22 端口，而非 FTP 的 21 端口，类似于 Unix/Linux 下的 sftp 命令（可选）
PuTTYtel.exe  | 远程联机客户端,仅仅使用 Telnet 协议（可选）
Plink.exe  | 命令行工具，执行远程服务器上的命令（可选）
Pageant.exe  | PuTTY、PSCP、Plink 的 SSH 认证代理，用这个可以不用每次都输入口令
PuTTYgen.exe  | RSA 和 DSA 密钥生成工具（必须）


* 在下载这这两个文件文件后，我们还需要需要要验证下载的文件的完
整性以保证这些下载的文件没有被发布者之外的第三方改动过。可以
下页面上提供的 MD5 文件来进行校验（具体校验方法 可以参考” 1
部署一个好的 Linux 系统 任务 3 获取安装镜像盘”）。
* PuTTY 是一个准绿色软件，不需要安装。说它绿色是因为直接就能
使用，完全没有任何的安装程序。准绿色是指 PuTTY 的所有配置都
保存到了注册表2。

**参考资料：**

http://my.oschina.net/fsmwhx/blog/143354

http://www.vpser.net/security/linux-ssh-authorized-keys-login.html

http://www.aiezu.com/system/linux/xshell_ssh_public-key_login.html

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004
