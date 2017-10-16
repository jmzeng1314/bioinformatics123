---
author: sam
comments: true
date: 2015-02-12 14:54:38+00:00
#layout: post
link: http://hugo.qinqianshan.com/too-many-levles-of-symbolic-links/
slug: too-many-levles-of-symbolic-links
title: Linux【12】-报错2-Too many levles of symbolic links
wordpress_id: 2097
categories:
- basic_description
tags:
- systemctl
---

服务器死机重启后总是会报错：
		
	Too many levles of symbolic links

<!-- more -->

解决办法：
		
	sudo systemctl restart autofs


原理：

未知
