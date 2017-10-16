---
author: sam
comments: true
date: 2017-04-20 02:43:01+00:00
#layout: post
link: http://hugo.qinqianshan.com/remote-control-putty-and-teamviewer/
slug: remote-control-putty-and-teamviewer
title: Linux【10】-远程连接3-内网穿透
wordpress_id: 298
categories:
- basic_description
tags:
- 内网
---

## 常用的内网穿透的方法

1. NATAPP 基于 ngrok 高速内网穿透
2. Ngrok 国内免费服务器
3. Sunny-Ngrok 内网转发
4. gotunnel.org
5. 花生壳软件-内网也能用！内网动态域名
6. 网络通内网端口映射


## 一、向日葵

虚拟专用网（vpn）被定义为通过一个公用网络建立一个临时的、安全的连接，是一条穿过混乱的公用网络的安全、稳定的隧道，使用这条隧道可以对数据进行几倍加密达到安全使用互联网的目的。其核心就是利用公共网络建立虚拟私有网，在连接在Internet上的位于不同地方的两个或多个企业内部网之间建立一条专有的通讯线路。通过向日葵VPN功能，就可以简捷的创建一个虚拟网络，可以帮助远程用户、公司分支机构、商业伙伴及供应商同公司的内部网建立可信的安全连接，并保证数据的安全传输。
<!-- more -->

**更多资料**

如何添加VPN http://service.oray.com/question/661.html

用向日葵建异地VPN http://network.chinabyte.com/391/13748891.shtml

### 一、简要说明

1.控制端和被控端下载安装向日葵客户端

	linux 下如何安装向日葵 http://service.oray.com/question/1379.html

	wget -c http://sunlogin.oray.com/zh_CN/download/download?id=16/
	tar -xzvf .tar.gz
	cd ./sunlogin
	sudo ./run.sh

2.添加主机 （http://sunlogin.oray.com/console/remote/），生成一个新的向日葵账号，然后被控端打开向日葵界面后输入这个
向日葵账号（内网ip:3008 网页中完成设置），就会生成一个虚拟的VPN账号

3.两边同时重启向日葵，然后ping 这个虚拟的vpn账号，如果能ping通，则证明OK

## 二、讨论

2.1.ping不通

1. 搭建VPN完成后VPN网络下的电脑互ping时，您需要开放这几个端口：UDP 3008、4008、7008-7024和TCP 443，如果还是ping不通，那就需要关闭防火墙。
http://service.oray.com/question/1938.html

2. 关键的一步：搭建VPN结束以后，向日葵需要重启

2.2 文件如何传送？

pycharm
服务器vpn ip, port 默认22，服务器账户以及密码

2.3. 重启以后自动开启
后续补充





参考资料：

https://www.v2ex.com/t/268495

