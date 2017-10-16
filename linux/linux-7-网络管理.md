---
author: sam
comments: true
date: 2017-06-01 07:44:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/net-management/
slug: net-management
title: Linux【7】-网络管理
wordpress_id: 1015
categories:
- basic_description
tags:
- ifconfig
---

## 一、查看和操作网络接口

查看网络接口是否启动并配置好，我们可以使用 ifconfig 命令来完成。首先
可以观察网络接口是否启动，只要简单键入 ifconfig 即可，假设主机中仅有一个
网络接口，并且网络接口启动并被正确配置，那么你可以在命令的输出中观察到
此网络接口的状态如下所示：

    [root@localhost ~]# ifconfig
    eth0 Link encap:Ethernet HWaddr 00:0C:29:47:B0:C7
     inet addr:192.168.71.128 Bcast:192.168.71.255 Mask:255.255.255.0
     UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
     RX packets:9776 errors:0 dropped:0 overruns:0 frame:0
     TX packets:3245 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000
     RX bytes:919008 (897.4 KiB) TX bytes:635590 (620.6 KiB)

    # link encap 表示网络连接的类型，Ethernet 是以太网
    # HWaddr 是网络接口的硬件地址
    # Inet addr 是网络接口的 ip 地址，Bcast 是网络的广播地址，Mask 是子网掩码
    # UP BROADCAST RUNNING MULTICAST 表示网络接口的运行状态
    # RX 和 TX 是网络接口收发包的情况
    # conllisions 表示以太网发生冲突的次数，如果该值过高，就要考虑网络硬件连接出可能
    # 发生了问题


    lo Link encap:Local Loopback
     inet addr:127.0.0.1 Mask:255.0.0.0
     inet6 addr: ::1/128 Scope:Host
     UP LOOPBACK RUNNING MTU:16436 Metric:1
     RX packets:1590 errors:0 dropped:0 overruns:0 frame:0
     TX packets:1590 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:0
     RX bytes:1884308 (1.7 MiB) TX bytes:1884308 (1.7 MiB)
    # lo 不是一个真正的网络接口，是一个特殊的模拟出来的网络接口，每个系统都有它，叫
    回环接口(loop)，其 ip 永远为 127.0.0.1，子网掩码为 255.0.0.0。回环接口为系统提供
    一个单机网络环境，一般用于网络程序的调试，许多程序的运行也需要这个接口。

如果网络接口因为其他原因没有启动，那么就只能够看到回环接口 lo 的相关
状态。你可以用“ifconfig –a ”查看到所有安装好的网络接口（无论接口有没有
被启动和配置好），此时可能情况有三种：

* 情况 1：仍然没有看到接口，那么就说明网卡硬件连接或者驱动出现问题，导致系统无法识别此网卡了，此时应该检查硬件或者重新安装驱动，
如果仍然无法识别，那么就需要考虑更换网卡。
* 情况 2：看到接口了，但是输出中没有 Inet addr、Bcast、Mask 等参数出现，那就表示网卡硬件没有问题，是接口没有配置好，此时就应该查
看并编辑相关配置文件对接口进行配置。
* 情况 3：看到接口了，并且接口一切正常，那么就可能是接口未开启，可

以使用 ifconfig 命令来开启接口：


    [root@localhost ~]# ifconfig eth0 up
    #如果要关闭接口，可以使用 ifconfig eth0 down

如果临时要改变网络接口的配置参数,也可以使用 ifconfig 命令:

    [root@localhost ~]# ifconfig eth0 172.168.19.54 netmask 255.255.255.0
    # 设置接口 eth0 网络地址为 172.168.19.54,子网掩码为 255.255.255.0
    
    [root@localhost ~]# ifconfig eth0 mtu 3000
    # 设置接口的最大网络传输单元 MTU 为 3000 字节
    
    [root@localhost ~]# ifconfig eth0
    eth0 Link encap:Ethernet HWaddr 00:0C:29:47:B0:C7
     inet addr: 172.168.19.54 Bcast: 172.168.19.255 Mask:255.255.255.0
     inet6 addr: fe80::20c:29ff:fe47:b0c7/64 Scope:Link
     UP BROADCAST RUNNING MULTICAST MTU:3000 Metric:1
     RX packets:631 errors:0 dropped:0 overruns:0 frame:0
     TX packets:297 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000
     RX bytes:62037 (60.5 KiB) TX bytes:42484 (41.4 KiB)
    [root@localhost ~]#

* 用 ifconfig 来改变接口的配置参数是临时的,也就是说,如果系统重启,
或者网络重启的话,这些改变的参数是不会保留下来的.
* 如果你在远程连接主机的话，不要轻易改动网络参数，这很可能导致
你掉线

**用法：ifconfig [接口名][参数]**

ifconfig 用于配置和显示系统网络接口。如没有给出参数， ifconfig 显示
当前所有有效接口的状态。如给定单个接口名作为参数，它只显示给出的
那个接口的状态； 如果给出一个 -a 参数，它会显示所有接口的状态，
包括那些停用的接口。

* 接口 网络接口名称，例如第一个以太接口 eth0 。
* up 此选项激活接口。
* down 此选项使接口关闭。
* [-]arp 允许或禁止在接口上使用 ARP 协议。
* [-]allmulti 允许或禁止 组播模式（all-multicast） 。 如果选用，则接口可以接收网络上的所有组播分组。
* metric N 将接口度量值设置为整数 N。 (译注：度量值表示在这个路径上发送一个分组的成本,就是通过多少个路由）
* mtu N 此选项设定接口的最大传输单元 MTU。
* netmask [子网掩码] 为接口设定 IP 网络掩码。缺省值通常是 A，B或 C 类的网络掩码 (由接口的 IP 地址推出)，但也可设为其它值。
* [-]broadcast [广播地址]如果给出了地址参数， 则可以为接口设定该协议的广播地址。 否则，为接口设置(或清除) IFF_BROADCAST 标志。
* IP 地址 为接口分配的 IP 地址。

重启网络服务

    service network restart

关闭接口
    
    ifconfig eth1 down 

## 二、配置TCP/IP网络接口

Linux 为 配 置 网 络 提 供 了 许 多 工 具 ， 其 中 有 图 形 界 面 的 （ 如
NetworkManager1）、也有伪图形界面（如 system-config-network 2）的。虽然
使用这些工具来配置网络会很方便，但是由于各个发行版本的 Linux 所提供的网
络配置工具很可能完全不同，并且通过命令行界面的远程登录也无法使用这些图
形界面的工具，所以我们并不打算使用工具来配置网络，而选择通过直接编辑相
关文件来配置网络参数。


在 Linux 系统中，网络是通过若干个文本文件进行配置的，需要编辑这些文
件来完成联网工作。系统中重要的有关网络配置文件为：/etc/services、
/etc/host.conf 、 /etc/nsswitch.conf 、 /etc/xinetd.conf /etc/modules.conf 、
/etc/sysconfig/network、/etc/hosts、/etc/resolv.conf 和/etc/sysconfig/ networkscripts/ifcfg-ethN。我们在这里要简要解释的后四个文件，也是我们任务中用到
的四个文件。

1 /etc/sysconfig/network 文件

/etc/sysconfig/network 文件用来为主机设定全局网络参数（也即不是针对单
个网络接口的参数）。

参数名 |参数值
------|-------
NETWORKING=< value> |< value> 值表示是否配置这个网络,可以是如下值: yes —配置网络； no — 不配置网络
HOSTNAME=< value>  | < value> 值为主机名，一般来说，这个主机名应该是一个完整的 FQDN ( Fully Qualified Domain Name)，也即主机名+域名，如 hostname.expample.com。但是主机名并非必须为 FQDN，你可以给主机取一个任意格式的名字
GATEWAY=< value>  | < value>值为网络的默认网关 IP 地址
 
2 /etc/sysconfig/network-scripts/ifcfg-ethN 文件

对于主机上的每个网络接口都需要进行配置，在 Linux 中每个网络接口都有一个独立的配置文件，文件名一般为：/etc/sysconfig/network-scripts/ifcfg-ethN，
其中 N 是一个数字，代表主机网络接口的序号，例如，主机中有多个网络接口，
那么第一个接口的配置文件就叫做 ifcfg-eth0，第二个接口的配置文件就叫做
ifcfg-eth1，依次类推。

     vim /etc/sysconfig/network-scripts/ifcfg-eth0

    BOOTPROTO=DHCP 改为 BOOTPROTO = static  表示不使用DHCP协议来配置网络接口，而是采用静态配置，也即手动指定网络接口的相关参数
    IPADDR=192.168.71.128 # 添加这一行，
    GATEWAY=192.168.71.1  #添加这一行
    NETMASK=255.255.255.0 #添加这一行
    DNS1=192. 168.71.2     #DNS服务器地址

重启网络服务

    service network restart

/etc/sysconfig/network 中的 GATEWAY 参数会被
/etc/sysconfig/network-scripts/ifcfg-eth0 中的 GATEWAY 参数覆盖，以最后启动的那一个为准  

ifconfig 来查看是否已经修改
ping  -c 来查看是否已经生效 (-c 测试次数)


    vim /etc/sysconfig/network
    
    HOSTNAME=localhost.localdomain 修改为 HOSTNAME=mooc.nbcc.cn

    vim /etc/hosts
    #添加一行，主机ip地址和主机名
    192.168.71.128  mooc.nbcc.cn

    service network restart 

    ping mooc.nbcc.cn

    如果能ping到主机，表示主机名设置无误

    最后还需要重启电脑，主机中很多服务都需要应用到这个主机名
    

网络接口配置文件控制每个网络接口的行为，当系统启动时就使用这些文件
来确定激活哪些网络接口和如何去配置这些接口。

注意，网络接口配置文件中的某些配置会影响到主机上其他网络接口甚至整
个整个主机网络配置。


参数名 |参数值
------|------
BOOTPROTO=< value>  |< value>值表示采用何种方式来配置主机网络参数,可以是如下值:1. none/static — 手动配置此主机网络； 2.bootp — 使用 BOOTP 协议动态配置主机网络参数.3. dhcp — 使用 dhcp 协议动态配置主机网络参数.
DEVICE=< value>    |< value>值表示物理设备的名称,也即网卡的名称
DEFROUTE=< value>  | < value>值表示是否将本网络接口作为网络的默认路由(default route),可以是如下值:1. yes — 是；2. no — 否.
DNS{1,2}=< value>  |< value>值表示 DNS 服务器的 IP 地址,可以添加两个 DNS服务器 DNS1 和 DNS2,如果 PEERDNS 参数值为 yes ,那么此 DNS 参数值将覆盖 /etc/resolv.conf 文件中的DNS 服务器配置.
HWADDR=< value>     |< value>值为太网卡的 MAC 地址,是如下格式一个 12 位的16 进制串 AA:BB:CC:DD:EE:FF. 如果主机上有两张以上的网卡这个参数就是必须的,可以保证不管网卡加载次序如何,设备名都不会改变,进而能够让正确的配置文件来对其进行配置.
IPADDR=< value>     | <  value>值为当前网络接口 IP 地址.
IPV4_FAILURE_FATAL=< value>  |< value>值指的是,当 IPv4 和 IPv6 配置都可用时,IPv4 配置失败是是否视为设备激活失败, 可以是如下值:1. yes — 是；2. no — 否.
IPV6INIT=< value>  |< value>值表示是否对配置主机的 IPv6 网络参数,可以是如下值:1. yes — 是；2. no — 否.
NETMASK=< value>    |< value>值为当前网络接口的子网掩码.
NM_CONTROLLED=< value>  |< value>值表示此网络接口是否允许 RHEL 和 CentOS 中的默认图形界面的网络配置工具 NetworkManager 来进行配置, 可以是如下值:1. yes — 是；2. no — 否.
ONBOOT=< value>    | < value>值表示是否在启动时激活此网络接口,可以是如下值: 1.yes — 是；2.no — 否.
PEERDNS=< value>   |< value>值表示是否允许接口配置文件中的 DNS 参数值覆盖 /etc/resolv.conf 文件中的 DNS 服务器配置,可以是如下值:1. yes — 是.2. no — 否.
PEERROUTES=< value> | SRCADDR=< value> 表示为从这个接口发出去的数据包指定另外一个源地址 < value> (而不是接口的 IP 地址).
USERCTL=< value>    | < value>值表示是否允许除 root 用户之外的用户配置此网络接口,可以是如下值:1. yes — 是;2. no — 否.

3 /etc/resolv.conf 和/etc/hosts 文件

该文件是 DNS 客户端配置文件，它的格式很简单，每行以一个关键字开头，
后接配置参数。最主要是 nameserver 关键字，如果没指定 nameserver 就找不
到 DNS 服务器，其它关键字都是可选的。

参数名 |参数值
------|------
nameserver =< value>  | < value>为 DNS 服务器的 IP 地址,总共可以指定 3 个 DNS服务器 IP 地址
domain=< value>  | < value>值表示本地域名
search=< value> |< value>值表示域名的搜索列表,默认情况下仅包括本地域名

下面是一个/etc/resolv.conf 文件的示例：

    domain ringkee.com
    search www.ringkee.com ringkee.com
    nameserver 202.96.128.86
    nameserver 202.96.128.166

/etc/hosts 文件是一个用于储存计算机网络中各节点信息的文件。这个文件
负责将主机名映射到相应的 IP 地址。hosts 文件通常用于补充或取代小型网络
中 DNS 的功能。和 DNS 不同的是，用户可以直接对/etc/hosts 文件进行控制。
在默认情况下, /etc/hosts 仅包括两行，分别用于指定 IPv4 网络中和 IPv6 网
络中回环接口的主机名（默认情况下为 localhost.localdomain）:

    # IP 地址
    127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1 localhost localhost.localdomain localhost6 localhost6.localdomain6

下面是一个/etc/resolv.conf 文件的示例：

    127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
    #以上两行建议保留
    # IP 地址 主机名(Hostname) 主机别名(Alias)
    208.164.186.1 deep.openna.com deep
    208.164.186.2 mail.openna.com mail
    208.164.186.3 web.openna.com web

**参考资料：**

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004