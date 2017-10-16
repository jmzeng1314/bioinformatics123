---
author: sam
comments: true
date: 2015-02-13 09:55:18+00:00
#layout: post
link: http://hugo.qinqianshan.com/received-message-is-too-long-1684366183/
slug: received-message-is-too-long-1684366183
title: Linux【12】-报错1-Received message is too long
wordpress_id: 674
categories:
- basic_description
---

通过SFTP连接服务器的时候，报错：

	
	Received message is too long: 1684366183
	 无法初始化SFTP协议。主机是SFTP服务器吗？


<!-- more -->

**解决办法：**
删除.bashrc/.bashrc_profile等下面的任何echo,cat,等语句。
=
我的是删除了多余的换行符

**参考资料：**

http://blog.sciencenet.cn/blog-522684-522675.html
