---
author: sam
comments: true
date: 2017-08-27 07:32:54+00:00
#layout: post
link: http://hugo.qinqianshan.com/chown-file-owner/
slug: chown-file-owner
title: Linux【2】-管理文件5-文件拥有者（chown）
wordpress_id: 1008
categories:
- basic_description
tags:
- chown
---

chown将指定文件的拥有者改为指定的用户或组，用户可以是用户名或者用户ID；组可以是组名或者组ID；文件是以空格分开的要改变权限的文件列表，支持通配符。系统管理员经常使用chown命令，在将文件拷贝到另一个用户的名录下之后，让用户拥有使用该文件的权限。

<!-- more -->
    
    chown [选项]... [所有者][:[组]] 文件...
    必要参数:
    
    　　　　-c 显示更改的部分的信息
    　　　　-f 忽略错误信息   
    　　　　-h 修复符号链接    
    　　　　-R 处理指定目录以及其子目录下的所有文件    
    　　　　-v 显示详细的处理信息   
    　　　　-deference 作用于符号链接的指向，而不是链接文件本身
    
    　　选择参数:
    
    　　　　--reference=<目录或文件> 把指定的目录/文件作为参考，把操作的文件/目录设置成参考文件/目录相同拥有者和群组   
    　　　　--from=<当前用户：当前群组> 只有当前用户和群组跟指定的用户和群组相同时才进行改变   
    　　　　--help 显示帮助信息
    　　　　--version 显示版本信息
    
    


改变拥有者和群组
    
    chown mail:mail log2012.log

改变文件拥有者
    
    chown mail： log2012.log  前面的是拥有者，后面的是群组

改变文件群组
    
    chown :mail log2012.log

改变指定目录以及其子目录下的所有文件的拥有者和群组
    
    chown -R -v root:mail test6


**参考资料：**

http://www.cnblogs.com/peida/archive/2012/12/04/2800684.html
