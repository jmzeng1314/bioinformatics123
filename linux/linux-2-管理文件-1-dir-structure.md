---
author: sam
comments: true
date: 2017-09-05 14:59:00+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-dir-structure/
slug: linux-dir-structure
title: Linux【2】-管理文件1-Linux目录结构
wordpress_id: 3096
categories:
- basic_description
---

Linux 文件系统包含排列在磁盘或其他区块存储设备的目录中的文件。与其他许多系统相同，Linux 系统中的目录也包含其他目录，即子目录。 Microsoft® Windows® 等系统的概念是在不同的驱动器盘符上（A:，C:，等）使用不同的文件系统，而 Linux 文件系统则截然不同，它是一个树形结构，以 / 目录作为根 目录。


    / 根目录
    bin //系统程序
    boot //内核和启动程序，所有和启动相关的文件都保存在这里
    grub //引导器相关文件
    dev //设备文件
    etc //系统软件的启动和配置文件，系统在启动过程中需要读取的文件都在这个目录。如LILO参数、用户账户和密码。
    home //用户的主目录。下面是自己定义的用户名的文件夹
    lib //系统程序库文件,这个目录里存放着系统最基本的动态链接共享库，类似于Windows下的system32目录，几乎所有的应用程序都需要用到这些共享库。
    media //挂载媒体设备，如光驱、U盘等
    mnt //目录是让用户临时挂载别的文件系统，如挂载Windows下的某个分区，ubuntu默认还是挂载在/media目录。
    opt //可选的应用软件包（很少使用）
    proc //这个目录是系统内存的映射，我们可以直接访问这个目录来获取系统信息。也就是说，这个目录的内容不在硬盘上而是在内存里。
    sbin //管理员系统程序
    selinux
    srv
    sys //udev用到的设备目录树，/sys反映你机器当前所接的设备
    tmp //临时文件夹
    usr //这是个最庞大的目录，我们要用到的很多应用程序和文件几乎都存放在这个目录下。]
    bin // 应用程序
    game //游戏程序
    include
    lib //应用程序的库文件
    lib64
    local //包含用户程序等
    sbin //管理员应用程序
    share //应用程序资源文件
    src //源代码
    var //动态数据，所有服务的登录文件或错误信息文件（log file）都在 /var/log下。此外一些数据库，如MYSQL则在/var/lib下，还有用户未读邮件的默认存放地点为/var/spool/mail。
    lost_found //磁盘修复文件，存放因非法关机而丢失

根目录下面的目录是可以任意添加的，但是一般不建议随便添加，作为linux系统的普通用户，一般在自己的``/home/user`` 下面操作。


