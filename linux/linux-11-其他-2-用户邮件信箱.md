---
author: sam
comments: true
date: 2016-09-11 01:10:36+00:00
#layout: post
link: http://hugo.qinqianshan.com/mail-linux-users-mail-box/
slug: mail-linux-users-mail-box
title: Linux【11】-其他2-用户邮件信箱（mail）
wordpress_id: 739
categories:
- basic_description
tags:
- mail
---

每个Linux主机上的用户都有一个mailbox，可以用来发邮件，一般mailbox都放在/var/spool/mail/

<!-- more -->

## 1.邮件主题：
    
    mail -s “test” ***@***.cn

这条命令的结果是发一封标题为test的空信给后面的邮箱，如果你有mta并且后面的邮箱不会挡这种可能莫名奇妙的信的时候，就能收到这封信了。

## 2.带内容

方法一：
    
    echo “mail content”|mail -s test ***@****.cn
    或者是 
    cat id.txt |mail -s test ***@****.cn


方法二：

文本编辑好内容再发送，用<后面接文件名
    
    mail -s "fjal" qqin@genowise.com <id_rsa.pub

方法三：

例如：
    
    mailtest@hotmail.com
    Subject : mail test
    :
    键入信文内容
    : :
    按下Ctrl+D 键或. 键结束正文。
    连按两次Ctrl+C键则中断工作，不送此信件。
    Cc( Carbon copy) : 复制一份正文，给其他的收信人。

## 3.带附件

（没用过，但方法可参见下面的）
如果你的系统里没有uuencode命令的话，请先安装sharutils(yum install sharutils)
uuencode 需要两个参数，第一个是你要发送的文件(们)，当然这个也可以用管道来做，第二个是显示的文件名
    
    uuencode /home/yfang/a.gif a.gif|mail -s pictest yangfang@fudan.edu.cn
    tar czf - /home/yfang/ | uuencode home.tgz |mail -s pictest yangfang@fudan.edu.cn

## 4. 检查所传送的电子邮件是否送出，或滞留在邮件服务器中
    
    /usr/lib/sendmail -bp

若屏幕显示为“Mail queue is empty” 的信息，表示mail 已送出。
若为其他错误信息，表示电子邮件因故尚未送出。

## 5.mail查看邮件

个人感觉没有网页好看，有点鸡肋，具体参见《鸟哥私房菜》。
基本用法：
    
    终端输入mail
    h 列出信件表头
    输入数字 就能看到对应的邮件
    d d+数字，删掉


**参考资料：**

http://www.cnblogs.com/JemBai/archive/2012/01/24/2329136.html （超赞）

《鸟哥私房菜》
