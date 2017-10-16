---
author: sam
comments: true
date: 2017-07-22 09:00:22+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-wildcard/
slug: linux-ln
title: Linux【2】-管理文件10-通配符
wordpress_id: 1038
categories:
- basic_description
tags:
- ln
---

通常，如需要对多个文件对象执行单一操作，而不需要像前面的递归操作一样对整个树进行操作。例如，你可能想要找出在某个目录下中创建的所有文本文件的修改时间，而不需要列出分散的文件。尽管这很容易在小目录中实现，但是对于大型文件系统则非常困难。

要解决这个问题，可以使用 bash shell 中内置的通配符(wildcard)支持。这种支持也称为“globbing”（因为它最初被实现为一个名为 /etc/glob 的程序），让你能够使用通配符模式指定多个文件。

包含任何 '?'、'*' 或 '[' 字符的字符串就是一个通配符模式。Globbing 是指 shell（或另一个程序）将这些模式扩展为一组匹配该模式的参数的过程。这种匹配按照如下方式完成：

字符	|含义|实例
----|-----|--- 
*  | 匹配 0 或多个字符|a*b a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb,  axyzb, a012b, ab。
? | 匹配任意一个字符 | a?b  a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb,  acb, a0b。
[list] | 	 匹配 list 中的任意单一字符 |a[xyz]b   a与b之间必须也只能有一个字符,但只能是 x 或 y 或 z, 如: axb,  ayb, azb。 
[!list] | 匹配除list 中的任意单一字符 | a[!0-9]b  a与b之间必须也只能有一个字符,但不能是阿拉伯数字, 如axb, aab,  a-b。 
[c1-c2] | 匹配 c1-c2 中的任意单一字符如：[0-9] [a-z] |a[0-9]b  0与9之间必须也只能有一个字符如a0b,  a1b... a9b。 
{string1,string2,...}  |匹配 sring1 或 string2 (或更多)其一字符串 | a{abc,xyz,123}b     a与b之间只能是abc或xyz或123这三个字符串之一。

## 例子
1.拷贝文件

    cp /tmp/file[0-9].txt -

2.删除a1或a2开头的文件夹

    rm -fr {a1,a2}*.sh

    
**参考资料：**

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004
