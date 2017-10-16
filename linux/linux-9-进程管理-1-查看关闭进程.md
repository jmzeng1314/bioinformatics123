---
author: sam
comments: true
date: 2017-05-05 04:24:53+00:00
#layout: post
link: http://hugo.qinqianshan.com/process-management-pstoppstreekill/
slug: process-management-pstoppstreekill
title: Linux【9】-进程管理1--ps/top/pstree/kill
wordpress_id: 249
categories:
- basic_description
tags:
- kill
- ps
- pstree
- top
---

程序是为了完成某种任务而设计的软件，比如拷贝命令“cp”就是程序，文本编辑工具 vi也是一个程序。而一个运行中的程序就被称为进程。进程的一个比较正式的定义是：在自身的虚拟地址空间运行的一个单独的程序。进程与程序是有区别的，进程不是程序，虽然它由程序产生。程序只是一个静态的指令集合，不占系统的运行资源；而进程是一个随时都可能发生变化的、动态的、使用系统运行资源的程序。

一个运行着的程序，可能包含多个进程。比如宁波城市职业技术学院的网站(www.nbcc.cn)用的 WWW 服务器是 Apache 服务器，当管理员启动服务后，可能会有好多人来访问，也就是说许多用户来同时请求 httpd 服务，Apache 服务器将会创建有多个 httpd 进程来对其进行服务。

## 一、进程分类
进程一般分为交互进程、批处理进程和守护进程三类。上述三种进程各有各的作用，使用场合也有所不同。

* 交互进程：由一个 shell 启动的进程。交互进程既可以在前台运行，也可以在后台运行。
* 批处理进程：这种进程和终端没有联系，是一个进程序列。
* 守护进程：Linux 系统启动时启动的进程，并在后台运行。

值得一提的是守护进程总是活跃的，一般是后台运行，守护进程一般是由系统在开机时通过一个叫做 init 的脚本自动激活启动或超级管理用户 root 来启动。

由于守护进程是一直运行着的，所以它所处的状态是等待请求处理任务。比如，无论我们是不是访问 www.nbcc.cn ，该主机上的 Apache 服务器(进程名叫做 httpd)都在运行，等待着用户来访问，也就是等待着任务处理。

## 二、进程的描述符

进程描述符（Process Descriptor），顾名思义，就是进程的描述，即用来描述进程的数据结构，可以理解为进程的属性。比如进程的状态、进程的标识（PID）等。

符号 | 含义
-----|------
USER | 启动进程用户
PID |进程标志号
PPID |父进程的 ID，父子进程的关系是管理和被管理的关系，当父进程终止时，子进程也随之而终止。但子进程终止，父进程并不一定终止。
%CPU  |运行该进程占用 CPU 的时间与该进程总的运行时间的比例
%MEM  |该进程占用内存和总内存的比例
VSZ | 占用的虚拟内存大小
RSS |进程占用的物理内存值
TTY | 进程建立时所对应的终端，“?”表示该进程不占用终端
STAT |进程的状态， 状态分为D，不可中断的睡眠；  R，就绪（在可运行队列中）；S，睡眠；T，被跟踪或停止；Z，终止（僵死）的进程W，没有足够的内存分页可分配；< 高优先序的进程；N，低优先序的进程；L，有内存分页分配并锁在内存体内（实时系统或 I/O）。+ 位于后台的进程； s 进程的领导者（在它之下有子进程）；
START | 为进程开始时间
TIME  |为执行的时间
COMMAND  |是对应的命令名


要对进程进行监测和控制，首先需要查看当前进程。ps 命令就是最常用、也
是相当强大的进程查看命令。根据显示的信息可以确定哪个进程正在运行、哪个
进程被挂起、进程已运行了多久、进程正在使用的资源、进程的相对优先级，以
及进程的标志号（PID）。所有这些信息对用户都很有用，对于系统管理员来说更
为重要。

我们在这里建立一个如下特殊的脚本文件作为例子：

    #! /bin/bash
    # filename bad.sh
    endless_loop()
    {
    echo -ne "i=0;
    while true
    do
    i=i+100;
    i=100
    done" | /bin/bash &
    }
    if [ $# != 1 ] ; then
     echo "请根据你主机 CPU(核心)个数输入一个整数： $0 <CPUs>"
     exit 1;
    fi
    for i in `seq $1`
    do
     endless_loop
     pid_array[$i]=$! ;
    done

如果主机有多颗 CPU 或者 CPU 有多个核心，可以选择消耗多颗 CPU（核
心）的计算资源。使用方法很简单，如可以输入“./bad.sh 8”来运行这个脚本，
其中参数 8 表示消耗 8 颗 CPU 的资源。

因为我们机器中的 CPU 是双核心，所以我们输入“./bad.sh 2”来模拟这个
恶意占用 CPU 的程序，执行了这个程序后，我们主机 CPU 就被大量占用，如
我们前面所说的，如果主机是个服务器，用户就会发现服务器的相应速度明显下
降甚至不响应了。那么这时我们就要干预一下了，首先我们要找出“罪魁祸首”
也就是找出哪些进程在不正常地大量占用 CPU。

下面我们就使用“ps aux --sort -%cpu”命令来获得主机上当前所有进程的
信息，并将输出按照 “%CPU”这个字段值降序排列：

    [root@localhost ~]# ps aux --sort -%cpu
    USER PID %CPU %MEM VSZ RSS TTY STAT START TIME
    COMMAND
    root 5066 99.8 0.0 106088 1164 pts/0 R Jan11 53:23 /bin/bash
    root 5068 99.7 0.0 106088 1160 pts/0 R Jan11 53:20 /bin/bash
    root 12 0.1 0.0 0 0 ? S Jan11 0:37 [events/1]
    root 1425 0.1 0.2 185276 4360 ? Sl Jan11 0:34 /usr/sbin/vmtoo
    root 1 0.0 0.0 19352 1556 ? Ss Jan11 0:01 /sbin/init
    ……此处省略若干行

显示的每一行均代表一个进程，每行都有 11 个字段，每个字段均为进程的
一个属性（在知识点 1 中有详细说明），我们要关心的是三个字段，第三个字段
“%CPU”、第八个字段“STAT”和第十个字段“TIME”， “%CPU”表示的是
进程占用的 CPU 百分比，我们可以看到这两个进程几乎占用全部 CPU，由于我
们输出按照 “%CPU”这个字段值降序排列，因此这两个进程排在输出的头两
位；并且由“STAT”可以得知，这两个进程正在运行中，而由“TIME”可以得
知这两个进程已经运行了 50 分钟了。显然这两个进程正是我们要找的“罪魁祸
首”。

我们用 ps 命令和 grep 命
令来查找名称中能带有 sshd 字样的进程。

    [root@localhost ~]# ps aux|grep sshd
    root 2032 0.0 0.0 64068 1184 ? Ss 15:03 0:00 /usr/sbin/sshd
    root 2502 0.0 0.2 97812 3904 ? Ss 15:04 0:00 sshd: root@pts/0
    root 2563 0.0 0.0 103252 848 pts/0 S+ 15:10 0:00 grep sshd
    [root@localhost ~]#

## 三、查看和操纵系统服务

在 Linux 中，有某些进程在系统启动时就开始在后台运行，除非程序出现异
常或者被人为终止，这些进程将一直运行，直到关机才结束。这些特殊后台进程
对系统和用户来说非常重要，甚至是不可或缺的。原因就在于这些进程都是一些
不中断的，随时接受请求的，为用户和系统提供某项特定功能的后台进程。

这些后台进程就被称为守护进程（daemons），正式地说，守护进程就是运行
在后台的一种特殊进程。它独立于控制终端并且周期性地执行某种任务或等待处
理某些发生的事件。它不需要用户输入就能运行而且为系统或者用户提供某种服
务（services）。

我们举两个例子来说明好了：比如 Linux 上的 web 服务器 Apache 的守护
进程进程叫做 httpd，启动以后会在后台监听 80 端口，当接收到客户端的请求的
时候用相应的网页（文件）响应客户端；又比如 Linux 上的计划任务守护进程叫
做 crond，启动以后每分钟都会去读取 /etc/crontab 配置文件来检查是否有计划
任务需要执行。

守护进程一般在系统启动时开始运行，除非强行终止，否则直到系统关机都
保持运行。守护进程经常以超级用户（root）权限运行，因为它们要使用特殊的
端口（1-1024）或访问某些特殊的资源。守护进程是非交互式程序，没有控制终
端，所以任何输出，无论是向标准输出设备 stdout 还是标准出错设备 stderr 的
输出都需要特殊处理。

用法：service [服务脚本名] [动作命令]
service 命令用于对系统服务进行操纵，比如启动（start）、停止（stop）、
重启（restart）、查看状态（status）等，service 命令本身是一个 shell 脚
本，它在/etc/init.d/目录查找指定的服务脚本，然后调用该服务脚本来完
成任务，sevice

* service 服务脚本名    打印指定服务的命令行使用帮助。
* service 服务脚本名 start 启动指定的系统服务
* service 服务脚本名 status 查看指定的系统服务状态
* service 服务脚本名 stop 停止指定的系统服务
* service 服务脚本名 restart 重新启动指定的系


chkconfig --list” 命令显示系统服务列表，以及这些服务在运
行级别 0 到 6 中已被启动（on）还是停止（off），
<!-- more -->

## 四、常用命令详解
### 1.ps

[root@www ~]# ps aux <==观察系统所有的程序数据
[root@www ~]# ps -lA <==也是能够观察所有系统的数据
[root@www ~]# ps axjf <==连同部分程序树状态

选项与参数：

     -A ：所有的 process 均显示出来，与 -e 具有同样的效用；
     -a ：显示所有用户的所有进程（包括其它用户）；
     -u ：按用户名和启动时间的顺序来显示进程；
     x ：通常与 a 这个参数一起使用，可列出较完整信息。显示无控制终端的进程；
     r 显示运行中的进程；

     输出格式规划：
     -l ：较长、较详细的将该 PID 的的信息列出；
     -j ：工作的格式 (jobs format)，用任务格式来显示进程；
     -f ：做一个更为完整的输出。用树形格式来显示进程；

    --sort [+|-] 字段名 按指定字段对 ps 输出进行排序，+表示升序，-表示降序， “+”字符是可选的，因为默认按升序排列
    
范例一：将目前属于您自己这次登入的 PID 与相关信息列示出来(只与自己的 bash 有关)

     [root@www ~]# ps -l
     F S UID PID PPID C PRI NI ADDR SZ WCHAN TTY TIME CMD
     4 S 0 13639 13637 0 75 0 - 1287 wait pts/1 00:00:00 bash
     4 R 0 13700 13639 0 77 0 - 1101 - pts/1 00:00:00 ps
     系统整体的程序运作是非常多的，但如果使用 ps -l 则仅列出与你的操作环境 (bash) 有关的程序而已， 亦即最上层的父程序会是你自己的 bash 而没有延伸到 init 这支程序去！那么 ps -l 秀出来的资料有哪些呢？ 我们就来观察看看：
     F：代表这个程序旗标 (process flags)，说明这个程序的总结权限，常见号码有：
     若为 4 表示此程序的权限为 root ；
     若为 1 则表示此子程序仅进行复制(fork)而没有实际执行(exec)。
     S：代表这个程序的状态 (STAT)，主要的状态有：
     R (Running)：该程序正在运作中；
     S (Sleep)：该程序目前正在睡眠状态(idle)，但可以被唤醒(signal)。
     D ：不可被唤醒的睡眠状态，通常这支程序可能在等待 I/O 的情况(ex>打印)
     T ：停止状态(stop)，可能是在工作控制(背景暂停)或除错 (traced) 状态；
     Z (Zombie)：僵尸状态，程序已经终止但却无法被移除至内存外。
     UID/PID/PPID：代表『此程序被该 UID 所拥有/程序的 PID 号码/此程序的父程序 PID 号码』
     C：代表 CPU 使用率，单位为百分比；
     PRI/NI：Priority/Nice 的缩写，代表此程序被CPU 所执行的优先级，数值越小代表该程序越快被 CPU 执行。详细的 PRI 与 NI 将在下一小节说明。
     ADDR/SZ/WCHAN：都与内存有关，ADDR 是 kernel function，指出该程序在内存的哪个部分，如果是个 running 的程序，一般就会显示『 - 』 / SZ 代表此程序用掉多少内存 / WCHAN 表示目前程序是否运作中，同样的， 若为 - 表示正在运作中。
     TTY：登入者的终端机位置，若为远程登录则使用动态终端接口 (pts/n)；
     TIME：使用掉的 CPU 时间，注意，是此程序实际花费 CPU 运作的时间，而不是系统时间；
     CMD：就是 command 的缩写，造成此程序的触发程序之指令为何。
     所以你看到的 ps -l 输出讯息中，他说明的是：『bash 的程序属于 UID 为 0 的使用者，状态为睡眠 (sleep)， 之所以为睡眠因为他触发了 ps (状态为 run) 之故。此程序的 PID 为 13639，优先执行顺序为 75 ， 下达 bash 所取得的终端接口为 pts/1 ，运作状态为等待 (wait) 。』这样已经够清楚了吧？ 您自己尝试解析一下那么 ps 那一行代表的意义为何呢？ ^_^


接下来让我们使用 ps 来观察一下系统内所有的程序状态吧！
观察系统所有程序： ps aux

范例二：列出目前所有的正在内存当中的程序：

     [root@www ~]# <strong>ps aux</strong>
     USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
     root 1 0.0 0.0 2064 616 ? Ss Mar11 0:01 init [5]
     root 2 0.0 0.0 0 0 ? S< Mar11 0:00 [migration/0]
     root 3 0.0 0.0 0 0 ? SN Mar11 0:00 [ksoftirqd/0]
     .....(中间省略).....
     root 13639 0.0 0.2 5148 1508 pts/1 Ss 11:44 0:00 -bash
     root 14232 0.0 0.1 4452 876 pts/1 R+ 15:52 0:00 ps aux
     root 18593 0.0 0.0 2240 476 ? Ss Mar14 0:00 /usr/sbin/atd
     你会发现 ps -l 与 ps aux 显示的项目并不相同！在 ps aux 显示的项目中，各字段的意义为：
     USER：该 process 属于那个使用者账号的？
     PID ：该 process 的程序标识符。
     %CPU：该 process 使用掉的 CPU 资源百分比；
     %MEM：该 process 所占用的物理内存百分比；
     VSZ ：该 process 使用掉的虚拟内存量(Kbytes)
     RSS ：该 process 占用的固定的内存量(Kbytes)
     TTY ：该 process 是在那个终端机上面运作，若与终端机无关则显示 ?，另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。
     STAT：该程序目前的状态，状态显示与 ps -l 的 S 旗标相同 (R/S/T/Z)
     START：该 process 被触发启动的时间；
     TIME ：该 process 实际使用 CPU 运作的时间。
     COMMAND：该程序的实际指令为何？
     一般来说，ps aux 会依照 PID 的顺序来排序显示，我们还是以 13639 那个 PID 那行来说明！该行的意义为『 root 执行的 bash PID 为 13639，占用了 0.2% 的内存容量百分比，状态为休眠 (S)，该程序启动的时间为 11:44 ， 且取得的终端机环境为 pts/1 。』与 ps aux 看到的其实是同一个程序啦！这样可以理解吗？ 让我们继续使用 ps 来观察一下其他的信息吧！

范例三：以范例一的显示内容，显示出所有的程序：

     [root@www ~]# ps -lA
     F S UID PID PPID C PRI NI ADDR SZ WCHAN TTY TIME CMD
     4 S 0 1 0 0 76 0 - 435 - ? 00:00:01 init
     1 S 0 2 1 0 94 19 - 0 ksofti ? 00:00:00 ksoftirqd/0
     1 S 0 3 1 0 70 -5 - 0 worker ? 00:00:00 events/0
     ....(以下省略)....
     # 你会发现每个字段与 ps -l 的输出情况相同，但显示的程序则包括系统所有的程序。

范例四：列出类似程序树的程序显示：

     [root@www ~]# ps axjf
     PPID PID PGID SID TTY TPGID STAT UID TIME COMMAND
     0 1 1 1 ? -1 Ss 0 0:01 init [5]
     .....(中间省略).....
     1 4586 4586 4586 ? -1 Ss 0 0:00 /usr/sbin/sshd
     4586 13637 13637 13637 ? -1 Ss 0 0:00 \_ sshd: root@pts/1
     13637 13639 13639 13639 pts/1 14266 Ss 0 0:00 \_ -bash
     13639 14266 14266 13639 pts/1 14266 R+ 0 0:00 \_ ps axjf
     .....(后面省略).....

看出来了吧？其实鸟哥在进行一些测试时，都是以网络联机进主机来测试的，所以啰，你会发现其实程序之间是有相关性的啦！ 不过，其实还可以使用 pstree 来达成这个程序树喔！以上面的例子来看，鸟哥是透过 sshd 提供的网络服务取得一个程序， 该程序提供 bash 给我使用，而我透过 bash 再去执行 ps axjf ！这样可以看的懂了吗？其他各字段的意义请 man ps (虽然真的很难 man 的出来！) 啰！

范例五：找出与 cron 与 syslog 这两个服务有关的 PID 号码？

     [root@www ~]# ps aux | egrep '(cron|syslog)'
     root 4286 0.0 0.0 1720 572 ? Ss Mar11 0:00 syslogd -m 0
     root 4661 0.0 0.1 5500 1192 ? Ss Mar11 0:00 crond
     root 14286 0.0 0.0 4116 592 pts/1 R+ 16:15 0:00 egrep (cron|syslog)
     # 所以号码是 4286 及 4661 这两个啰！就是这样找的啦！
     除此之外，我们必须要知道的是『僵尸 (zombie) 』程序是什么？ 通常，造成僵尸程序的成因是因为该程序应该已经执行完毕，或者是因故应该要终止了， 但是该程序的父程序却无法完整的将该程序结束掉，而造成那个程序一直存在内存当中。 如果你发现在某个程序的 CMD 后面还接上 时，就代表该程序是僵尸程序啦，例如：
     apache 8683 0.0 0.9 83384 9992 ? Z 14:33 0:00 /usr/sbin/httpd
     当系统不稳定的时候就容易造成所谓的僵尸程序，可能是因为程序写的不好啦，或者是使用者的操作习惯不良等等所造成。 如果你发现系统中很多僵尸程序时，记得啊！要找出该程序的父程序，然后好好的做个追踪，好好的进行主机的环境优化啊！ 看看有什么地方需要改善的，不要只是直接将他 kill 掉而已呢！不然的话，万一他一直产生，那可就麻烦了！ @_@
     事实上，通常僵尸程序都已经无法控管，而直接是交给 init 这支程序来负责了，偏偏 init 是系统第一支执行的程序， 他是所有程序的父程序！我们无法杀掉该程序的 (杀掉他，系统就死掉了！)，所以啰，如果产生僵尸程序， 而系统过一阵子还没有办法透过核心非经常性的特殊处理来将该程序删除时，那你只好透过 reboot 的方式来将该程序抹去了！



### 2. top：动态观察程序的变化
 
与ps 不同之处在于，top 是动态监视系统进程的工具，在默认情况下，top 命令每
3 秒钟刷新一次。接下来我们就用 top 来查看一下当前系统进程运行情况，注意
到总的 CPU 的使用率和两个特殊进程的 CPU 占用率

相对于 ps 是撷取一个时间点的程序状态， top 则可以持续侦测程序运作的状态！使用方式如下：

[root@www ~]# top [-d 数字] | top [-bnp]
选项与参数：

     -d ：后面可以接秒数，就是整个程序画面更新的秒数。预设是 5 秒；
     -b ：以批次的方式执行 top ，还有更多的参数可以使用喔！
     通常会搭配数据流重导向来将批次的结果输出成为档案。
     -n ：与 -b 搭配，意义是，需要进行几次top 的输出结果。
     -p ：指定某些个 PID 来进行观察监测而已。
     在 <strong>top 执行过程当中可以使用的按键指令</strong>：
     ? ：显示在 top 当中可以输入的按键指令；
     P ：以 CPU 的使用资源排序显示；
     M ：以 Memory 的使用资源排序显示；
     N ：以 PID 来排序喔！
     T ：由该 Process 使用的 CPU 时间累积 (TIME+) 排序。
     k ：给予某个 PID 一个讯号 (signal)
     r ：给予某个 PID 重新制订一个 nice 值。
     q ：离开 top 软件的按键。
     其实 top 的功能非常多！可以用的按键也非常的多！可以参考 man top 的内部说明文件！ 鸟哥这里仅是列出一些鸟哥自己常用的选项而已。接下来让我们实际观察一下如何使用 top 与 top 的画面吧！
    
范例一：每两秒钟更新一次 top ，观察整体信息：
     
     [root@www ~]# top -d 2
     top - 17:03:09 up 7 days, 16:16, 1 user, load average: 0.00, 0.00, 0.00
     Tasks: 80 total, 1 running, 79 sleeping, 0 stopped, 0 zombie
     Cpu(s): 0.5%us, 0.5%sy, 0.0%ni, 99.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
     Mem: 742664k total, 681672k used, 60992k free, 125336k buffers
     Swap: 1020088k total, 28k used, 1020060k free, 311156k cached
     <==如果加入 k 或 r 时，就会有相关的字样出现在这里喔！
     PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
     14398 root 15 0 2188 1012 816 R 0.5 0.1 0:00.05 top
     1 root 15 0 2064 616 528 S 0.0 0.1 0:01.38 init
     2 root RT -5 0 0 0 S 0.0 0.0 0:00.00 migration/0
     3 root 34 19 0 0 0 S 0.0 0.0 0:00.00 ksoftirqd/0
     top 也是个挺不错的程序观察工具！但不同于 ps 是静态的结果输出， top 这个程序可以持续的监测整个系统的程序工作状态。 在默认的情况下，每次更新程序资源的时间为 5 秒，不过，可以使用 -d 来进行修改。 top 主要分为两个画面，上面的画面为整个系统的资源使用状态，基本上总共有六行，显示的内容依序是：
     第一行(top...)：这一行显示的信息分别为：
     目前的时间，亦即是 17:03:09 那个项目；
     开机到目前为止所经过的时间，亦即是 up 7days, 16:16 那个项目；
     已经登入系统的用户人数，亦即是 1 user项目；
     系统在 1, 5, 15 分钟的平均工作负载。我们在第十六章谈到的 batch 工作方式为负载小于 0.8 就是这个负载啰！代表的是 1, 5, 15 分钟，系统平均要负责运作几个程序(工作)的意思。 越小代表系统越闲置，若高于 1 得要注意你的系统程序是否太过繁复了！
     第二行(Tasks...)：显示的是目前程序的总量与个别程序在什么状态(running, sleeping, stopped, zombie)。 比较需要注意的是最后的 zombie 那个数值，如果不是 0 ！好好看看到底是那个process 变成僵尸了吧？
     第三行(Cpus...)：显示的是 CPU 的整体负载，每个项目可使用 ? 查阅。需要特别注意的是 %wa ，那个项目代表的是 I/O wait， 通常你的系统会变慢都是 I/O 产生的问题比较大！因此这里得要注意这个项目耗用 CPU 的资源喔！ 另外，如果是多核心的设备，可以按下数字键『1』来切换成不同 CPU 的负载率。
     第四行与第五行：表示目前的物理内存与虚拟内存 (Mem/Swap) 的使用情况。 再次重申，要注意的是 swap 的使用量要尽量的少！如果 swap 被用的很大量，表示系统的物理内存实在不足！
     第六行：这个是当在 top 程序当中输入指令时，显示状态的地方。
     至于 top 下半部分的画面，则是每个 process 使用的资源情况。比较需要注意的是：
     PID ：每个 process 的 ID 啦！
     USER：该 process 所属的使用者；
     PR ：Priority 的简写，程序的优先执行顺序，越小越早被执行；
     NI ：Nice 的简写，与Priority 有关，也是越小越早被执行；
     %CPU：CPU 的使用率；
     %MEM：内存的使用率；
     TIME+：CPU 使用时间的累加；
     top 预设使用 CPU 使用率 (%CPU) 作为排序的重点，如果你想要使用内存使用率排序，则可以按下『M』， 若要回复则按下『P』即可。如果想要离开 top 则按下『 q 』吧！如果你想要将 top 的结果输出成为档案时， 可以这样做：

范例二：将 top 的信息进行 2 次，然后将结果输出到 /tmp/top.txt

     [root@www ~]# top -b -n 2 > /tmp/top.txt
     # 这样一来，嘿嘿！就可以将 top 的信息存到/tmp/top.txt 档案中了。
     这玩意儿很有趣！可以帮助你将某个时段 top 观察到的结果存成档案，可以用在你想要在系统背景底下执行。 由于是背景底下执行，与终端机的屏幕大小无关，因此可以得到全部的程序画面！那如果你想要观察的程序 CPU 与内存使用率都很低，结果老是无法在第一行显示时，该怎办？我们可以仅观察单一程序喔！如下所示：
    
范例三：我们自己的 bash PID 可由 $$ 变量取得，请使用 top 持续观察该 PID

     [root@www ~]# echo $$
     13639 <==就是这个数字！他是我们 bash 的 PID
     [root@www ~]# <span style="color: #ff0000;">top -d 2 -p 13639</span>
     top - 17:31:56 up 7 days, 16:45, 1 user, load average: 0.00, 0.00, 0.00
     Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
     Cpu(s): 0.0%us, 0.0%sy, 0.0%ni,100.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
     Mem: 742664k total, 682540k used, 60124k free, 126548k buffers
     Swap: 1020088k total, 28k used, 1020060k free, 311276k cached

    PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
     13639 root 15 0 5148 1508 1220 S 0.0 0.2 0:00.18 bash
     看到没！就只会有一支程序给你看！很容易观察吧！好，那么如果我想要在 top 底下进行一些动作呢？ 比方说，修改 NI 这个数值呢？可以这样做：
    
范例四：承上题，上面的 NI 值是 0 ，想要改成 10 的话？

     # 在范例三的 top 画面当中直接按下 r 之后，会出现如下的图样！
     top - 17:34:24 up 7 days, 16:47, 1 user, load average: 0.00, 0.00, 0.00
     Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
     Cpu(s): 0.0%us, 0.0%sy, 0.0%ni, 99.5%id, 0.0%wa, 0.0%hi, 0.5%si, 0.0%st
     Mem: 742664k total, 682540k used, 60124k free, 126636k buffers
     Swap: 1020088k total, 28k used, 1020060k free, 311276k cached
     PID to renice: 13639 <==按下 r 然后输入这个 PID 号码
     PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
     13639 root 15 0 5148 1508 1220 S 0.0 0.2 0:00.18 bash
     在你完成上面的动作后，在状态栏会出现如下的信息：
     Renice PID 13639 to value: 10 <==这是 nice 值
     PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
     接下来你就会看到如下的显示画面！
     top - 17:38:58 up 7 days, 16:52, 1 user, load average: 0.00, 0.00, 0.00
     Tasks: 1 total, 0 running, 1 sleeping, 0 stopped, 0 zombie
     Cpu(s): 0.0%us, 0.0%sy, 0.0%ni,100.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st
     Mem: 742664k total, 682540k used, 60124k free, 126648k buffers
     Swap: 1020088k total, 28k used, 1020060k free, 311276k cached
     PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
     13639 root 26 10 5148 1508 1220 S 0.0 0.2 0:00.18 bash
     看到不同处了吧？底线的地方就是修改了之后所产生的效果！一般来说，如果鸟哥想要找出最损耗 CPU 资源的那个程序时，大多使用的就是 top 这支程序啦！然后强制以 CPU 使用资源来排序 (在 top 当中按下 P 即可)， 就可以很快的知道啦！ ^_^。多多爱用这个好用的东西喔！

### 3.pstree
    
[root@www ~]# pstree [-A|U] [-up]
     
选项与参数：

     -A ：各程序树之间的连接以 ASCII 字符来连接；
     -U ：各程序树之间的连接以万国码的字符来连接。在某些终端接口下可能会有错误；
     -p ：并同时列出每个 process 的 PID；
     -u ：并同时列出每个 process 的所属账号名称。

范例一：列出目前系统上面所有的程序树的相关性：

     [root@www ~]# pstree -A
     init-+-acpid
     |-atd
     |-auditd-+-audispd---{audispd} <==这行与底下一行为 auditd 分出来的子程序
     | `-{auditd}
     |-automount---4*[{automount}] <==预设情况下，相似的程序会以数字显示
     ....(中间省略)....
     |-sshd---sshd---bash---pstree <==就是我们指令执行的那个相依性！
     ....(底下省略)....
     # 注意一下，为了节省版面，所以鸟哥已经删去很多程序了！
    
范例二：承上题，同时秀出 PID 与 users

     [root@www ~]# pstree -Aup
     init(1)-+-acpid(4555)
     |-atd(18593)
     |-auditd(4256)-+-audispd(4258)---{audispd}(4261)
     | `-{auditd}(4257)
     |-automount(4536)-+-{automount}(4537) <==程序相似但 PID 不同！
     | |-{automount}(4538)
     | |-{automount}(4541)
     | `-{automount}(4544)
     ....(中间省略)....
     |-sshd(4586)---sshd(16903)---bash(16905)---pstree(16967)
     ....(中间省略)....
     |-xfs(4692,xfs) <==因为此程序拥有者并非执行pstree 者！所以列出账号
     ....(底下省略)....
     # 在括号 () 内的即是 PID 以及该程序的 owner 喔！不过，由于我是使用
     # root 的身份执行此一指令，所以属于 root 的程序就不会显示出来啦！
     如果要找程序之间的相关性，这个 pstree 真是好用到不行！直接输入 pstree 可以查到程序相关性，如上表所示，还会使用线段将相关性程序链接起来哩！ 一般链接符号可以使用 ASCII 码即可，但有时因为语系问题会主动的以 Unicode 的符号来链接， 但因为可能终端机无法支持该编码，或许会造成乱码问题。因此可以加上 -A 选项来克服此类线段乱码问题。

由 **pstree** 的输出我们也可以很清楚的知道，所有的程序都是依附在 init 这支程序底下的！ 仔细看一下，这支程序的 PID 是一号喔！因为他是由 Linux 核心所主动呼叫的第一支程序！所以 PID 就是一号了。 这也是我们刚刚提到僵尸程序时有提到，为啥发生僵尸程序需要重新启动？ 因为 init 要重新启动，而重新启动 init 就是 reboot 啰！

如果还想要知道 PID 与所属使用者，加上 -u 及 -p 两个参数即可。我们前面不是一直提到， 如果子程序挂点或者是老是砍不掉子程序时，该如何找到父程序吗？呵呵！用这个 pstree 就对了！ ^_^


### 4.kill

用法：kill [-signal] pid

kill 命令终止一个进程。

* pid：进程的 PID 号
* -signal：表示向进程发出的信号，如果没有指定任何信号，默认发送的信号为 SIGTERM（-15），可将指定进程终止。若仍无法终止该进程，可使用更强力的 SIGKILL（-9）信号尝试强制终止进程。

代号  |名称  |内容
----- |------|-----
1 | SIGHUP | 启动被终止的程序，可让该PID 重新读取自己的配置文件，类似重新启动
2  | SIGINT  | 相当于用键盘输入[ctrl]-c 来中断一个程序的进行
9 | SIGKILL  |代表强制中断一个程序的进行，如果该程序进行到一半， 那么尚未完成的部分可能会有『半产品』产生，类似vim会有 .filename.swp 保留下来。
15  | SIGTERM | 以正常的结束程序来终止该程序。由于是正常的终止， 所以后续的动作会将他完成。不过，如果该程序已经发生问题，就是无法使用正常的方法终止时， 输入这个 signal 也是没有用的。
19  | SIGSTOP  | 相当于用键盘输入[ctrl]-z 来暂停一个程序的进行
上面仅是常见的 signal 而已，更多的讯号信息请自行 man 7 signal 吧！一般来说，你只要记得『1, 9, 15』这三个号码的意义即可。那么我们如何传送一个讯号给某个程序呢？就透过 kill 或 killall 吧！

 
 kill 可以帮我们将这个 signal 传送给某个工作(%jobnumber) 或者是某个 PID (直接输入数字)。 要再次强调的是： kill 后面直接加数字与加上 %number 的情况是不同的！ 这个很重要喔！因为工作控制中有 1 号工作，但是 PID 1 号则是专指『 init 』这支程序！你怎么可以将 init 关闭呢？ 关闭 init ，你的系统就当掉了啊！所以记得那个 % 是专门用在工作控制的喔！ 我们就活用一下 kill 与刚刚上面提到的 ps 来做个简单的练习吧！
    
例题：
以 ps 找出 syslog 这个程序的 PID 后，再使用 kill 传送讯息，使得 syslog 可以重新读取配置文件。
 
答： 由于需要重新读取配置文件，因此 signal 是 1 号。至于找出 syslog 的 PID 可以是这样做：
 
     ps aux | grep 'syslog' | grep -v 'grep'| awk '{print $2}'

接下来则是实际使用 kill -1 PID，因此，整串指令会是这样：

     kill -SIGHUP $(ps aux|grep 'syslog'|grep -v 'grep'|awk '{print $2}')

如果要确认有没有重新启动 syslog ，可以参考登录档的内容，使用如下指令查阅：

     tail -5 /var/log/messages

 如果你有看到类似『Mar 19 15:08:20 www syslogd 1.4.1: restart』之类的字样，就是表示syslogd 在 3/19 有重新启动 (restart) 过了！ 了解了这个用法以后，如果未来你想要将某个莫名其妙的登入者的联机删除的话，就可以透过使用 pstree -p 找到相关程序， 然后再以 kill -9 将该程序删除，该条联机就会被踢掉了！这样很简单吧！


### 5.killall

    killall -signal 指令名称

由于 kill 后面必须要加上 PID (或者是 job number)，所以，通常 kill 都会配合 ps, pstree 等指令，因为我们必须要找到相对应的那个程序的 ID 嘛！但是，如此一来，很麻烦～有没有可以利用『下达指令的名称』来给予讯号的？举例来说，能不能直接将 syslog 这个程序给予一个 SIGHUP 的讯号呢？可以的！用 killall 吧！

    [root@www ~]# killall [-iIe] [command name]

选项与参数：

     -i ：interactive 的意思，交互式的，若需要删除时，会出现提示字符给用户；
     -e ：exact 的意思，表示『后面接的command name 要一致』，但整个完整的指令
     不能超过 15 个字符。
     -I ：指令名称(可能含参数)忽略大小写。

范例一：给予 syslogd 这个指令启动的 PID 一个 SIGHUP 的讯号

     [root@www ~]# killall -1 syslogd
     # 如果用 ps aux 仔细看一下，syslogd 才是完整的指令名称。但若包含整个参数，
     # 则 syslogd -m 0 才是完整的呢！

范例二：强制终止所有以 httpd 启动的程序

     [root@www ~]# killall -9 httpd

范例三：依次询问每个 bash 程序是否需要被终止运作！

     [root@www ~]# killall -i -9 bash
     Kill bash(16905) ? (y/N) n <==这个不杀！
     Kill bash(17351) ? (y/N) y <==这个杀掉！
     # 具有互动的功能！可以询问你是否要删除bash 这个程序。要注意，若没有 -i 的参数，
     # 所有的 bash 都会被这个 root 给杀掉！包括 root 自己的 bash 喔！ ^_^
    
总之，要删除某个程序，我们可以使用 PID 或者是启动该程序的指令名称， 而如果要删除某个服务呢？呵呵！最简单的方法就是利用 killall ， 因为他可以将系统当中所有以某个指令名称启动的程序全部删除。 举例来说，上面的范例二当中，系统内所有以 httpd 启动的程序，就会通通的被删除啦！ ^_^

**讨论：**

1.删掉某个用户的所有进程的方法（该用户为sam）
    
    方法一：
    pkill -u sam
    方法二：
    killall -u sam
    方法三：
    ps -ef | grep sam | awk '{ print $2 }' | sudo xargs kill -9
    方法四：
    pgrep -u sam | sudo xargs kill -9

2.杀掉跟某个程序有关的进程
        
    pkill -fe genoprime_web    
    杀死所有命令中出现geoprime_web的进程

**参考资料：**

http://bbs.chinaunix.net/thread-1974593-1-1.html

http://linux.vbird.org/linux_basic/0440processcontrol.php#process_1

http://www.ttlsa.com/linux-command/4-method-to-kill-user-all-process/

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004