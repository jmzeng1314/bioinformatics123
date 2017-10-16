---
author: sam
comments: true
date: 2017-06-12 07:44:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/disk-management/
slug: disk-management
title: Linux【6】-硬盘管理
wordpress_id: 1015
categories:
- basic_description
tags:
- mount
- fdisk
---

## 一、查看硬盘

在硬盘装上后，在分区之前，用 fdisk –l 就可以看到这块硬盘了，fdisk –l 会输出一些有关这块硬盘的基本信息，如下所示：

    [root@localhost ~]# fdisk –l 硬盘名/分区名
    ….#此处省略若干行
    Disk /dev/sdb: 536.9 GB, 536870912000 bytes
    255 heads, 63 sectors/track, 65270 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x2f7e945a
    …#此处省略若干行
    [root@localhost ~]#

要理解 fdisk –l 输出的关于磁盘的基本信息，我们首先需要简要了解一下机
械式磁盘（HDD）的物理构成：我们需要知道的概念包括：

* 盘片（platter）：一个机械式磁盘一般由若干磁性盘片组成；
* 磁头（head）：每个盘片一般有上下两面，盘片的两个面均能存储数据，因此需要两个磁头；
* 磁道（track）：每个盘片沿着半径的方向可以划分为很多同心圆，这些圆圈被称为磁道（track），磁道是从盘片外圈往内圈编号 0 磁道，1 磁道...，靠近
主轴的同心圆用于停靠磁头，不存储数据；
* 扇区（sector）：每个磁道被分成若干等份，每一份就被称为一个扇区，扇区是硬盘数据存储的最小单位；
* 柱面（cylinder）数：不同盘片上具有相同磁道编号的同心圆组成面就称作柱面，所以磁盘柱面数就等于磁盘单个盘片上的磁道数。柱面是硬盘分区的最小单位.因此，一个磁盘的容量 = 磁头数 ×柱面数 × 每道扇区数 × 每扇区字节数。

所以，上面这段 fdisk –l 输出就是告诉我们：这个名为/dev/sdb 的磁盘容量
总共为 536870912000 字节（536.9 GB），磁盘有 255 个磁头，每个磁道有 63
个扇区，总共有 65270 个柱面（磁道）。其中每个扇区容量为 512 字节（这个磁
盘的最小逻辑和物理单元，也是这个磁盘的读写的最小单元）, 因此每个柱面的
容量为 255×63×512 字节=8225280 字节。

当然在对磁盘进行分区( Partition )前，磁盘是无法用来存储数据的。事实上，
对磁盘进行分区的作用就是告诉操作系统磁盘可存储数据的区域，也即每个分区
的起始柱面和结束柱面。这些分区信息都包括在一个叫做分区表（Partition Table）
的数据结构中。

分区表是一块磁盘中最重要的数据，分区表一般存储在磁盘的 0 磁道上面
（所以当一个硬盘的 0 磁道发生了物理损坏的时，这个硬盘就基本宣告寿终正
寝了），硬盘仅仅为分区表保留了 64 个字节的存储空间，而每个分区的参数占据
16 个字节，故主引导扇区中总计可以存储 4 个分区的数据，，这就是主分区
(Primary)+扩展分区(Extended) 最多只能有四个的原因了。

## 二、分区和格式化硬盘

略

## 三、手动挂载分区
我们前面提到过，Linux 中的文件系统都是树形结构，所有的文件系统结合
起来就形成一个大的目录树，这个目录树的“根”就是根目录“/”。根分区在开
机的时候就自动挂载在根目录上。我们创建好的文件系统同样也要挂载到这个目
录树上才能被我们使用。挂载点一定是目录，这个目录就是访问该文件系统的入
口。
所以我们必须将我们刚刚格式化好的分区挂载到目录树上去，我们首先来挂
载第一个分区/dev/sdb1。

    [root@localhost ~]# mkdir /Log #我们的挂载点
    [root@localhost ~]# mount -t ext4 /dev/sdb1 /Log
    # -t 指明挂载的分区文件系统类型，/dev/sdb1 指明挂载的分区名，/Log 指明挂载点
    [root@localhost ~]# mount
    /dev/sda2 on / type ext4 (rw)
    proc on /proc type proc (rw)
    sysfs on /sys type sysfs (rw)
    devpts on /dev/pts type devpts (rw,gid=5,mode=620)
    tmpfs on /dev/shm type tmpfs (rw,rootcontext="system_u:object_r:tmpfs_t:s0")
    none on /proc/sys/fs/binfmt_misc type binfmt_misc (rw)
    sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw)
    /dev/sdb1 on /Log type ext4 (rw)
    # 用 mount 命令不跟参数也可以查看当前挂载的设备，主要是设备名，设备挂载点和设
    备# 的访问属性
    [root@localhost ~]# df
    文件系统 1K-块 已用 可用 已用% 挂载点
    /dev/sda2 18577148 3451884 14181600 20% /
    tmpfs 1026952 88 1026864 1% /dev/shm
    /dev/sdb1 103216920 192116 97781592 1% /Log
    umpo# 设备挂载点
    [root@localhost ~]# umount /dev/sdb1
    [root@localhost ~]# df
    文件系统 1K-块 已用 可用 已用% 挂载点
    /dev/sda2 18577148 3451884 14181600 20% /
    tmpfs 1026952 88 1026864 1% /dev/shm
    # 可以用 umount 命令卸载一个设备

* 尽量将分区挂载空目录上
* 不要重复挂载分区

**用法：mount [选项] 分区/设备名 挂载目录**

挂载文件系统

* -t ：与 mkfs 的选项非常类似的，可以加上文件系统种类来指定欲
挂载的类型常见的 Linux 支持类型有：ext2、ext3、ext4、vfat、
reiserfs、iso9660(光盘格式), nfs, cifs, smbfs(此三种为网络文件系统
类型)。
* -L ：系统除了利用设备名 (例如 /dev/hdc6) 之外，还可以利用文
件系统的标签(Label)来进行挂载。
* -l ：单纯的输入 mount 会显示目前挂载的信息。加上 -l 可列出目前挂载的设备的标签
* -a ：依照配置文件 /etc/fstab，将所有尚未挂载的设备都挂载上来
* -o ：后面可以接一些挂载时额外加上的参数，如读写权限等：

-o 选项后接的额外参数 |意义
------|------
ro, rw |挂载文件系统成为只读(ro) 或可擦写(rw)
async, sync |此文件系统是否使用同步写入 (sync) 或异步(async) 的内存机制，请参考文件系统运行方式。默认为 async。
auto, noauto |允许此分区被 mount -a 自动挂载(auto)
dev, nodev | 是否允许此分区上可创建设备文件， dev 为可允许
suid, nosuid | 是否允许此分区含有 suid/sgid 的文件格式？
exec, noexec  | 是否允许此分区拥有可运行二进制可执行文件
user, nouser | 是否允许 普通用户挂载此分区，一般来说，仅有 root 可以进行普通用户也能够对此 挂载这个分区。
defaults  |默认值为：rw, suid, dev, exec, auto, nouser,和 async
remount  |重新挂载

**用法：df [选项]**

显示所有文件系统 i 节点和磁盘块的使用情况。

* -a 显示所有挂载的文件系统的磁盘使用情况（包括虚拟的不占用硬
盘空间的文件系统，如/proc 文件系统）。
* -k 以 k 字节为单位显示。
* -i 显示 inode 信息，而不是块。
* -t 显示各指定类型的文件系统的磁盘空间使用情况。
* -x 列出不是某一指定类型文件系统的磁盘空间使用情况（与 t 选项
相反）。
* -T 显示文件系统类型

**用法：umount [选项] 分区/设备名|挂载目录**

卸载文件系统

* -f 强制卸载

## 四、自动挂载分区

vim /etc/fstab


序号 |字段名 |解释
------|-------|-------
字段 1  | 设备名/UUID/设备标签 | etc/fstab 文件的第一个字段指定了要挂载的设备，可以是一个/dev 目录下的设备文件，例如：/dev/sdb1 或者/dev/cdrom 之类的。也可以通过设备标签或者所谓的 UUID 来指定要挂载的设备如‘LABEL=pa_disk’ 或者 ‘UUID=3e6be9de-8139-11d1-9106-a43f08d823a6’。用设备标签或者设备的 UUID 来指定要挂载的设备更加安全和稳定(后面会详细分析)。
字段 2 |挂载点 |指定了挂载设备的目录，对于 swap，挂载点为none；对于挂载目录包含空格的情况，用“\040”来表示空格
字段 3 |挂载分区文件系统类型 | 定义了该设备上的文件系统，一般常见的文件类型为 ext2、ext3 ext4 (Linux 设备的常用文件类型)、或者 iso9600（光盘文件系统类型）等.如果这个字段定义为swap，这条纪录将关联到一个用于交换目的的文件或分区。如果这个字段定义为ignored，这行将被忽略。这对于显示目前没有使用的分区非常有用。
字段 4 | 挂载参数 | 指定加载该设备的文件系统是需要使用的特定参数选项，多个参数是由逗号分隔开来。
字段 5  |Dump 参数 |该选项被"dump"命令使用来检查一个文件系统是否应该进行 dump 备份，若不需要就设置该字段为 0，如果需要每天备份的，这个字段就应该为1，如果需要不定期备份的的 这个字段就应该为 2.
字段 6 | 启动检查分区的次序 | 该字段被 fsck 命令所使用来确定进行在系统重启进行文件系统检查时的顺序，对于根分区“/”这个值应设为 1，其它文件系统可以设为 2，在同一个物理硬盘内的文件系统应该被顺序检测，而不同硬盘中的文件系统则应该同时检测以充分利用系统的并行性。如果最后一个字段值为 0 或没有设置，fsck 程序装跳过此文件系统的检测。

e2label 可以给分区加上标签(label)，然后就可以使用标签来挂载/卸载该分区。
这对于有很多磁盘的系统，并且磁盘次序可能会发生变化的情况，会很有帮助。
假设有下列情况出现：主机上有若干块可以热插拔的硬盘，在主机运行过程中，
这些硬盘需要经常插拔，因此硬盘的次序包括硬盘的设备名会经常改变(如
/etc/sdb 在下次可能会变成/etc/sdd)，那么如何才能保证在/etc/fstab 文件中将设
备能够挂载到正确的挂载点上呢？

这是就可以用 e2label 给分区设置一个标签名，在/etc/fstab 中利用标签而不
是设备名来自动挂载分区.

    [root@localhost ~]# e2label /dev/sdb1 “log_disk”
    [root@localhost ~]# e2label /dev/sdb1
    log_disk

    #
    # /etc/fstab
    # Created by anaconda on Thu Nov 15 10:29:31 2012
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    UUID=7791d2ce-c9dd-4780-bffc-4c9d5d1467c5 / ext4
    defaults 1 1
    UUID=3e45eea0-bf5c-406f-84c0-a38cf9b9d2ba swap swap
    defaults 0 0
    tmpfs /dev/shm tmpfs defaults 0 0
    devpts /dev/pts devpts gid=5,mode=620 0 0
    sysfs /sys sysfs defaults 0 0
    proc /proc proc defaults 0 0
    Label=log_disk /Log ext4 defaults 0 2
    /dev/sdb5 /Project/pa ext4 defaults 1 2
    /dev/sdb6 /Project/pb vfat defaults 1 2
    /dev/sdb7 /Backup ext4 defaults 1 2

用法：e2label 分区设备名 [标签名]

用来设定或显示 ext2 或 ext3 分区的卷标。

注意：新设定的标签名称。不能超过 16 字符


当然这样挂载分区也是有缺点的，因为有可能两个分区的标签会重名，这系
统就无法确定到底要挂载哪个分区了，事实上，最保险的方法是通过分区设备的
UUID 来挂载文件系统。

UUID 全称是 Universally Unique Identifier，也即便说，每个分区有一个唯
一的 UUID 值。准确来说，UUID 是一个标识你系统中的存储设备的字符串，其
目的是帮助使用者唯一的确定系统中的所有存储设备，不管它们是什么类型的。
它可以标识光盘驱动器，USB 存储设备以及系统中的硬盘分区等。

与设备名和设备标签相比，UUID 有如下优点：

* 它是真正的唯一标志符。UUID 为系统中的存储设备提供唯一的标识字符串，
与分区标签不同，系统中不会出现有两个设备的 UUID 相同的情况
* 它总是不变的。分区的设备名称并非总是相同的，它们依赖于启动时内核加
载模块的顺序。如果你在插入了 USB 盘时启动了系统，而下次启动时又把
它拔掉了，就有可能导致设备名分配不一致。但是 UUID 总是不变的，无论
如何插拔，如何调换设备次序。

我们可以通过 blkid 命令来列出系统中所有存储设备的 UUID：

    [root@localhost ~]# blkid
    /dev/sda2: UUID="7791d2ce-c9dd-4780-bffc-4c9d5d1467c5" TYPE="ext4"
    /dev/sda1: UUID="3e45eea0-bf5c-406f-84c0-a38cf9b9d2ba" TYPE="swap"
    /dev/sdb1: LABEL="log_disk" UUID="feda157d-c51a-4f8b-8264-545ecfc06c21"
    TYPE="ext4"
    /dev/sdb5: UUID="19523fce-666e-4ec9-a53e-21723437eeb1" TYPE="ext4"
    /dev/sdb6: UUID="5156-CFF4" TYPE="vfat"
    /dev/sdb7: UUID="e2e2044b-34a1-4397-ba30-ad06bd964596" TYPE="ext4"

注意，UUID 依据分区不同，长度和格式都可能会不相同。

    #
    # /etc/fstab
    # Created by anaconda on Thu Nov 15 10:29:31 2012
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    UUID=7791d2ce-c9dd-4780-bffc-4c9d5d1467c5 / ext4
    defaults 1 1
    UUID=3e45eea0-bf5c-406f-84c0-a38cf9b9d2ba swap swap
    defaults 0 0
    tmpfs /dev/shm tmpfs defaults 0 0
    devpts /dev/pts devpts gid=5,mode=620 0 0
    sysfs /sys sysfs defaults 0 0
    proc /proc proc defaults 0 0
    UUID= feda157d-c51a-4f8b-8264-545ecfc06c21 /Log ext4 defaults
    0 2
    /dev/sdb5 /Project/pa ext4 defaults 1 2
    /dev/sdb6 /Project/pb vfat defaults 1 2
    /dev/sdb7 /Backup ext4 defaults 1 2


**参考资料：**

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004