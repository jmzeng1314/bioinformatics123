---
author: sam
comments: true
date: 2017-05-02 03:25:06+00:00
#layout: post
link: http://hugo.qinqianshan.com/manual-release-proc-sys-vm-drop-linux-memory-caches/
slug: manual-release-proc-sys-vm-drop-linux-memory-caches
title: Linux【9】-进程管理4-手工释放linux内存
wordpress_id: 105
categories:
- basic_description
tags:
- 内存
---

当在Linux下频繁存取文件后，物理内存会很快被用光，当程序结束后，内存不会被正常释放，而是一直作为caching。这个问题，貌似有不少人在问，不过都没有看到有什么很好解决的办法。那么我来谈谈这个问题。

# <!-- more -->一、通常情况

先来说说free命令：
    
    [root@server ~]# free -m
    total used free shared buffers cached
    Mem: 249 163 86 0 10 94
    -/+ buffers/cache: 58 191
    Swap: 511 0 511
    
    其中：
    total 内存总数
    used 已经使用的内存数
    free 空闲的内存数
    shared 多个进程共享的内存总额
    buffers Buffer Cache和cached Page Cache 磁盘缓存的大小
    -buffers/cache 的内存数:used – buffers – cached
    +buffers/cache 的内存数:free + buffers + cached
    可用的memory=free memory+buffers+cached。

有了这个基础后，可以得知，我现在used为163MB，free为86MB，buffer和cached分别为10MB，94MB。
那么我们来看看,如果我执行复制文件,内存会发生什么变化.

    [root@server ~]# cp -r /etc ~/test/
    [root@server ~]# free -m
    total used free shared buffers cached
    Mem: 249 244 4 0 8 174
    -/+ buffers/cache: 62 187
    Swap: 511 0 511

在我命令执行结束后，used为244MB，free为4MB，buffers为8MB，cached为174MB，天呐，都被cached吃掉了。别紧张，这是为了提高文件读取效率的做法。

为了提高磁盘存取效率，Linux做了一些精心的设计，除了对dentry进行缓存（用于VFS，加速文件路径名到inode的转换），还采取了两种主要Cache方式：Buffer Cache和Page Cache。前者针对磁盘块的读写，后者针对文件inode的读写。这些Cache有效缩短了 I/O系统调用（比如read，write，getdents）的时间。

那么有人说过段时间，linux会自动释放掉所用的内存。等待一段时间后，我们使用free再来试试，看看是否有释放？
    
    [root@server test]# free -m
    total used free shared buffers cached
    Mem: 249 244 5 0 8 174
    -/+ buffers/cache: 61 188
    Swap: 511 0 511
    似乎没有任何变化。（实际情况下，内存的管理还与Swap有关）

那么我能否手动释放掉这些内存呢？回答是可以的！

# 二、手动释放缓存

/proc是一个虚拟文件系统，我们可以通过对它的读写操作做为与kernel实体间进行通信的一种手段。也就是说可以通过修改/proc中的文件，来对当前kernel的行为做出调整。那么我们可以通过调整/proc/sys/vm/drop_caches来释放内存。操作如下：

切换到管理员权限
su
密码：

我当时密码弄不清楚了，然后sudo pwd ，重新修改了一下密码结果就好了，这里面的原因木有搞清楚啊

    [root@server test]# cat /proc/sys/vm/drop_caches
    0

首先，/proc/sys/vm/drop_caches的值，默认为0。

    [root@server test]# sync

手动执行sync命令（描述：sync 命令运行 sync 子例程。如果必须停止系统，则运行sync 命令以确保文件系统的完整性。sync 命令将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件）。数据一般是先从硬盘调到内存来被CPU进行处理，sync是一个非常牛逼的命令，他可以将放在内存中暂时没有写入硬盘的数据写入到硬盘中，避免数据的丢失。在关机的时候默认的有这个命令，如果是不正常关机的话，开机后也需要多进行这么个几次命令，好避免运行过程中在内存的数据被调用出来。

    [root@server test]# echo 3 > /proc/sys/vm/drop_caches
  
    如果上面报错： bash: echo: 写错误: 无效的参数
    解决办法：echo 1 >/proc/sys/vm/drop_caches
    其实就是讲数字换一下，一般不会报错,所以前面这步就可以没有
    
    [root@server test]# cat /proc/sys/vm/drop_caches
    3
    将/proc/sys/vm/drop_caches值设为3
    [root@server test]# free -m
    total used free shared buffers cached
    Mem: 249 66 182 0 0 11
    -/+ buffers/cache: 55 194
    Swap: 511 0 511
    再来运行free命令，会发现现在的used为66MB，free为182MB，buffers为0MB，cached为11MB。那么有效的释放了buffer和cache。
    
    ◎ 有关/proc/sys/vm/drop_caches的用法在下面进行了说明
    /proc/sys/vm/drop_caches (since Linux 2.6.16)
    Writing to this file causes the kernel to drop clean caches,dentries and inodes from memory, causing that memory to become free.
    
    To free pagecache, use 
    echo 1 > /proc/sys/vm/drop_caches;

    tofree dentries and inodes, use 
    echo 2 > /proc/sys/vm/drop_caches;
    
    to free pagecache, dentries and inodes, use 
    echo 3 >/proc/sys/vm/drop_caches.

Because this is a non-destructive operation and dirty objects are not freeable, the user should run sync first.

# 三、我的意见

上述文章就长期以来很多用户对Linux内存管理方面的疑问，给出了一个比较“直观”的回复，我更觉得有点像是核心开发小组的妥协。
对于是否需要使用这个值，或向用户提及这个值，我是有保留意见的：

1. 从man可以看到，这值从2.6.16以后的核心版本才提供，也就是老版的操作系统，如红旗DC 5.0、RHEL 4.x之前的版本都没有；
2. 若对于系统内存是否够用的观察，我还是原意去看swap的使用率和si/so两个值的大小；
用户常见的疑问是，为什么free这么小，是否关闭应用后内存没有释放？

但实际上，我们都知道这是因为Linux对内存的管理与Windows不同，free小并不是说内存不够用了，应该看的是free的第二行最后一个值：

    -/+ buffers/cache: 58 191

这才是系统可用的内存大小。
实际项目中告诉我们，如果因为是应用有像内存泄露、溢出的问题，从swap的使用情况是可以比较快速可以判断的，但free上面反而比较难查看。相反，如果在这个时候，我们告诉用户，修改系统的一个值，“可以”释放内存，free就大了。用户会怎么想？不会觉得操作系统“有问题”吗？

所以说，我觉得既然核心是可以快速清空buffer或cache，也不难做到（这从上面的操作中可以明显看到），但核心并没有这样做（默认值是0），我们就不应该随便去改变它。
一般情况下，应用在系统上稳定运行了，free值也会保持在一个稳定值的，虽然看上去可能比较小。

当发生内存不足、应用获取不到可用内存、OOM错误等问题时，还是更应该去分析应用方面的原因，如用户量太大导致内存不足、发生应用内存溢出等情况，否则，清空buffer，强制腾出free的大小，可能只是把问题给暂时屏蔽了。

我觉得，排除内存不足的情况外，除非是在软件开发阶段，需要临时清掉buffer，以判断应用的内存使用情况；或应用已经不再提供支持，即使应用对内存的时候确实有问题，而且无法避免的情况下，才考虑定时清空buffer。（可惜，这样的应用通常都是运行在老的操作系统版本上，上面的操作也解决不了）。

**参考资料：**

http://www.linuxany.com/archives/814.html
