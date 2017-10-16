---
author: sam
comments: true
date: 2017-06-29 10:35:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/tmux/
slug: tmux
title: Linux【4】-文本编辑3-tmux
wordpress_id: 442
categories:
- basic_description
tags:
- tmux
---

tmux是指通过一个终端登录远程主机并运行后，在其中可以开启多个控制台的终端复用软件


## <!-- more -->一、简介


安装：

mac下tmux的安装

    
    brew install tmux


[brew的安装](http://hugo.qinqianshan.com/brew-mac-apt-get-like-functionality/)


## 二、tmux快捷键


<table class="inline " >
<tbody >
<tr class="row0" >

<td colspan="2" class="col0 centeralign" >Ctrl+b
</td>

<td class="col2" >激活控制台；此时以下按键生效
</td>
</tr>
<tr class="row1" >

<td rowspan="9" class="col0" >系统操作
</td>

<td class="col1 centeralign" >?
</td>

<td class="col2" >列出所有快捷键；按q返回
</td>
</tr>
<tr class="row2" >

<td class="col0 centeralign" >d
</td>

<td class="col1" >脱离当前会话；这样可以暂时返回Shell界面，输入tmux attach能够重新进入之前的会话
</td>
</tr>
<tr class="row3" >

<td class="col0 centeralign" >D
</td>

<td class="col1" >选择要脱离的会话；在同时开启了多个会话时使用
</td>
</tr>
<tr class="row4" >

<td class="col0 centeralign" >Ctrl+z
</td>

<td class="col1" >挂起当前会话
</td>
</tr>
<tr class="row5" >

<td class="col0 centeralign" >r
</td>

<td class="col1" >强制重绘未脱离的会话
</td>
</tr>
<tr class="row6" >

<td class="col0 centeralign" >s
</td>

<td class="col1" >选择并切换会话；在同时开启了多个会话时使用
</td>
</tr>
<tr class="row7" >

<td class="col0 centeralign" >:
</td>

<td class="col1" >进入命令行模式；此时可以输入支持的命令，例如kill-server可以关闭服务器
</td>
</tr>
<tr class="row8" >

<td class="col0 centeralign" >[
</td>

<td class="col1" >进入复制模式；此时的操作与vi/emacs相同，按q/Esc退出
</td>
</tr>
<tr class="row9" >

<td class="col0 centeralign" >~
</td>

<td class="col1" >列出提示信息缓存；其中包含了之前tmux返回的各种提示信息
</td>
</tr>
<tr class="row10" >

<td rowspan="10" class="col0" >窗口操作
</td>

<td class="col1 centeralign" >c
</td>

<td class="col2" >创建新窗口
</td>
</tr>
<tr class="row11" >

<td class="col0 centeralign" >&
</td>

<td class="col1" >关闭当前窗口(退出了tumx)
</td>
</tr>
<tr class="row12" >

<td class="col0 centeralign" >数字键
</td>

<td class="col1" >切换至指定窗口
</td>
</tr>
<tr class="row13" >

<td class="col0 centeralign" >p
</td>

<td class="col1" >切换至上一窗口
</td>
</tr>
<tr class="row14" >

<td class="col0 centeralign" >n
</td>

<td class="col1" >切换至下一窗口
</td>
</tr>
<tr class="row15" >

<td class="col0 centeralign" >l
</td>

<td class="col1" >在前后两个窗口间互相切换
</td>
</tr>
<tr class="row16" >

<td class="col0 centeralign" >w
</td>

<td class="col1" >通过窗口列表切换窗口
</td>
</tr>
<tr class="row17" >

<td class="col0 centeralign" >,
</td>

<td class="col1" >重命名当前窗口；这样便于识别
</td>
</tr>
<tr class="row18" >

<td class="col0 centeralign" >.
</td>

<td class="col1" >修改当前窗口编号；相当于窗口重新排序
</td>
</tr>
<tr class="row19" >

<td class="col0 centeralign" >f
</td>

<td class="col1" >在所有窗口中查找指定文本
</td>
</tr>
<tr class="row20" >

<td rowspan="14" class="col0" >面板操作
</td>

<td class="col1 centeralign" >”
</td>

<td class="col2" >将当前面板平分为上下两块
</td>
</tr>
<tr class="row21" >

<td class="col0 centeralign" >%
</td>

<td class="col1" >将当前面板平分为左右两块
</td>
</tr>
<tr class="row22" >

<td class="col0 centeralign" >x
</td>

<td class="col1" >**关闭当前面板**
</td>
</tr>
<tr class="row23" >

<td class="col0 centeralign" >!
</td>

<td class="col1" >将当前面板置于新窗口；即新建一个窗口，其中仅包含当前面板
</td>
</tr>
<tr class="row24" >

<td class="col0 centeralign" >Ctrl+方向键
</td>

<td class="col1" >以1个单元格为单位移动边缘以调整当前面板大小
</td>
</tr>
<tr class="row25" >

<td class="col0 centeralign" >Alt+方向键
</td>

<td class="col1" >以5个单元格为单位移动边缘以调整当前面板大小
</td>
</tr>
<tr class="row26" >

<td class="col0 centeralign" >Space
</td>

<td class="col1" >在预置的面板布局中循环切换；依次包括even-horizontal、even-vertical、main-horizontal、main-vertical、tiled
</td>
</tr>
<tr class="row27" >

<td class="col0 centeralign" >q
</td>

<td class="col1" >显示面板编号
</td>
</tr>
<tr class="row28" >

<td class="col0 centeralign" >o
</td>

<td class="col1" >在当前窗口中选择下一面板
</td>
</tr>
<tr class="row29" >

<td class="col0 centeralign" >方向键
</td>

<td class="col1" >移动光标以选择面板
</td>
</tr>
<tr class="row30" >

<td class="col0 centeralign" >{
</td>

<td class="col1" >向前置换当前面板
</td>
</tr>
<tr class="row31" >

<td class="col0 centeralign" >}
</td>

<td class="col1" >向后置换当前面板
</td>
</tr>
<tr class="row32" >

<td class="col0 centeralign" >Alt+o
</td>

<td class="col1" >逆时针旋转当前窗口的面板
</td>
</tr>
<tr class="row33" >

<td class="col0 centeralign" >Ctrl+o
</td>

<td class="col1" >顺时针旋转当前窗口的面板
</td>
</tr>
</tbody>
</table>


## 三、具体操作


由于 tmux 允许随时随地断开或重新接入会话（Session），所以最大的作用就是在远程服务器上持久地保存工作状态。一个终端可以有多个会话，一个会话可以有多个窗口

[Ctrl+b]+ ? 显示快捷键帮助

**分离会话（detach）**

    
    [Ctrl+b] [d]  返回终端界面


**看到可用的会话**

tmux ls
0: 1 windows (created Wed Dec 9 16:29:12 2015) [186x60]
1: 4 windows (created Mon Dec 14 18:38:23 2015) [186x60]

**连接到会话0**

    
    tmux attach-session -t 0
    简写 tmux attach -t <目标会话名>
    tmux a -t <目标会话名>


**创建会话**

    
    tmux new-session -s dev 创建一个会话，取名为 dev
    tmux new -s session-name （缩写）


**关闭会话**

    
    tmux kill-session -t session-name



    
    <strong>创建新窗口</strong>
    C-b c 
    <strong>修改当前窗口名称</strong>
    
    c-b ,



    
    Ctrl +b 后  用page up 和page down可以来上下翻页；Esc 退出翻页的状态
    


tmux下无法滚屏很烦人。
一句话就解决了: CTRL + B 再按

    
    <span style="color: #ff0000;">[</span>


这时就可以用光标键来操作翻页滚屏,然后小写字母推出翻页模式

Ctrl + C 退出



如果需要兼容vim的操作方式，那么在~/.tmux.conf加上一行:

    
    setw -g mode-keys vi


Ctrl +b 后  用z来将一个窗口放大，然后再按z，就返回多窗口模式


### 3.7 显示时间



    
    Ctrl B   t


tmux的手册非常详尽，请输入**man tmux**后阅读


## 四、报错：



    
    sessions should be nested with care, unset $TMUX to force


解决办法：在terminal里执行： unset TMUX，然后再执行tmux attach就可以了



参考资料：
http://blog.csdn.net/hcx25909/article/details/7602935
http://www.cnblogs.com/wangkangluo1/archive/2012/04/15/2450347.html
https://wiki.freebsdchina.org/software/t/tmux
http://blog.jobbole.com/87278/
http://blog.chinaunix.net/uid-26285146-id-3252286.html
