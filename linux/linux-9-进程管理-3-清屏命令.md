---
author: sam
comments: true
date: 2017-05-03 09:52:11+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-terminal-garbled-solution/
slug: linux-terminal-garbled-solution
title: Linux【9】-进程管理3-清屏命令(clear/reset)
wordpress_id: 2163
categories:
- basic_description
tags:
- reset
---

## clear
 
这个命令将会刷新屏幕，本质上只是让终端显示页向后翻了一页，如果向上滚动屏幕还可以看到之前的操作信息。一般都会用这个命令。
 
## reset
 
这个命令将完全刷新终端屏幕，之前的终端输入操作信息将都会被清空，这样虽然比较清爽，但整个命令过程速度有点慢，使用较。

## 讨论

终端界面乱码：有时我们不小心用 cat 了二进制导致终端界面乱码，因为二进制文件中多有 控制码；

解决方法是用 reset 终端复位命令解决问题：
    
    reset

<!-- more -->

**参考资料：**

http://blog.csdn.net/shenwansangz/article/details/52812878

http://blog.csdn.net/wu407797466/article/details/7835896
