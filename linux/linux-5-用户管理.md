---
author: sam
comments: true
date: 2017-06-27 07:44:07+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-user-management/
slug: linux-user-management
title: Linux【5】-用户管理--adduser/groupadd/passwd
wordpress_id: 1014
categories:
- basic_description
tags:
- passwd
- usermod
---

## 一、用户和用户组
UID 是确认用户权限的标识，用户登录系统所处的角色是通过 UID 来实现的，而非用户名，因此，每个用户的 UID 必须是唯一的。UID 的唯一性是要管理员来确保的，其实通过修改/etc/passwd 文件，可以让任何用户的 UID 的值为 0，但是让几个用户共用一个 UID 是危险的，而普
通用户和 root 共用一个 UID ，那是尤其危险的事情，因为这事实上就造成了系统管理权限的混乱。

用户：

* 管理员用户 root 0
* 虚拟用户 1-499
* 普通用户，常用用户

CentOS 系统会把前 499 个 UID 预留出来，给系统虚拟用户占用，虚拟用户一般是系统安装时就有的，是为了完成系统任务所必须的用户，但虚拟用户是
不能登录系统的，比如 ftp、nobody、adm、rpm、bin、shutdown 等。

添加普通用户时的 UID 从 500 开始的，GID （用户组）也是从 500 开始，至于其它系统，有的系统可能会把前999 UID和GID预留出来；以各个系统中/etc/login.defs文件中的 UID_MIN 的最小值为准； CentOS 系统 login.defs 的 UID_MIN 是500，而 UID_MAX 值为 60000，也就是说我们通过 useradd 添加的普通用户的UID 的值一般都是在 500 到 60000 之间。

ID 和 UID 类似，是一个正整数或 0，GID 从 0 开始，GID 为 0 的组让系统付予给 root 用户组；系统会预留一些较靠前的 GID 给系统虚拟用户（也被称为
伪装用户）之用；每个系统预留的 GID 都有所不同，比如 CentOS 预留了 500个，我们添加新用户组时，用户组是从 500 开始的；查看系统添加用户组默认
的 GID 范围应该查看 /etc/login.defs 中的 GID_MIN 和 GID_MAX 值。

* Linux 是真正意义上的多用户操作系统，用户（user）在系统中是分角色的，由于角色不同，权限和所完成的任务也不同。
* 通俗来说，用户组（group）就是具有相同权限的用户的集合体。
* 用户和用户组的对应关系可以是是一对一、多对一、一对多或多对多，一个用户可以从属于多个用户组，一个用户组也可以包含多个用户，但是在同一时间，一个用户的主用户组（primary group）或者说有效用户组(effective group)只能有一个。主要是用户在创建文件时，为了这个文件从属用户组。
* 系统管理员能在 Linux 系统中建若干普通用户。每个用户都有自己的家目录，对自己家目录中的文件有完全的操控权限，对系统有有限的权限，在默认情况下，每个新建的普通用户都从属于一个单独的和用户名同名的用户组。
* 当然用户的概念理解还不仅仅于此，在 Linux 系统中还有一些用户是用来完成特定任务的，称为虚拟用户，这类用户不具有登录系统的能力，但却是系统运行不可缺少的用户，比如 bin、daemon、adm、ftp、
mail、nobody 等。我们的浏览器，就是 nobody 用户，我们匿名访问ftp 时，会用到用户 ftp。这类用户都系统自身拥有的，而非后来添加的，当然我们也可以添加虚拟用户。

## 二、查看用户和用户组

查看用户可以使用两个命令 id 和 finger，这两个命令各有测重，id 工具更测
重用户、用户所归属的用户组、UID 和 GID 的查看；而 finger 测重用户信息
的查询，比如用户名（登录名）、家目录、登录 SHELL 类型等等。

id [用户名]

查看某个指定用户的 UID，其初始用户组的 GID，以及它所从属的其他
用户组，如果不指定用户名，则是查看调用者的相关信息。

finger 用户名
查询用户相关信息的，比如用户名（登录名）、家目录、登录 SHELL 类
型等等

## 三、 切换用户身份-su

工作环境下，默认登录的用户一般来说（同时也应该）是一个普通用户，而
普通用户是没有管理其他用户的权力的，在一般情况下，所有用户都必须通过
root 用户来建立。这时的解决办法有两个，一是注销当前普通用户，重新以 root
用户登录，但这种办法并不方便；二是不退出当前用户，可以用 su (switch user)命令切换到
root 下进行下面两个任务，等任务完成后再退出 root。当然通过临时身份切换是
一种比较好的办法。


* 系统平日操作使用普通用户是一个好习惯，在有需要时, 才切换为
root 进行操作，以避免一时头脑糊涂或者手指抽筋，做出键入“ rm -
rf /” 命令等不可挽救的事情来;
* 用较低权限执行系统服务能够保证系统安全，有的时候，我们必须要
以某些系统账号运行程序。 举例来说， Linux 主机上面的 WWW 服
务器 Apache，我们可以额外建立一个名为 apache 的用户来启动
Apache 守护进程，如此一来，如果这个程序被黑，至少整个系统不
至于都被黑了。

    su [选项] [用户名]

在不退出当前登录的情况下，临时切换用户身份。
选项：
    
     - 或者-l 在切换用户的同时切换到相应用户的登录环境，包括家目录，SHELL 定义等。
     -l 选项可以简写为 -，也即“ su -l root” 等价于“su - root”
    如果 su 不带[用户名]参数，默认为切换到 root 用户，也即 “su - ” 等价于“su - root”
    如果 root 向普通用户切换不需要密码，而普通用户切换到其它任何用户都需要密码验证

* su 的确方便，只要把 root 的密码交给任何一个普通用户，他都能切
换到 root 来完成所有的系统管理工作；但通过 su 也带来了不安全因
素；例如系统有 10 个用户，而且都参与管理。如果这 10 个用户都
涉及到 root 权限的运用，作为系统管理员如果想让其它用户通过 su
来切换到 root，必须把 root 权限密码都告诉这 10 个用户。这在一定
程度上就对系统的安全造成了威胁。“没有不安全的系统，只有不安
全的人”，我们绝对不可能保证这 10 个用户都能按正常操作规程来管
理系统，其中任何一人对系统操作的重大失误，都可能导致不可挽回
的后果；
* 所以 su 在多人参与的系统管理中，并不是最好的选择，su 只适用于
一两个人参与管理的系统。在其他情况下，一般会选择使用 sudo（请
参考“sudo 和/etc/sudoers 目录”）；
* root 密码应该掌握在少数人手中，这绝对是 Linux 系统管理员的真理！

一般用户的密码忘了，这个比较容易解决，切换到 root 帮助他修改就可以了；
你忘记了 root 密码，这就比较麻烦了。最直接的一种补救方式就是重启，
在本地进入单人维护模式后，用 passwd 命令修改密码：

1. 开机读秒的时候按下任意键就会出现选单画面
2. 按下 e 进入 grub 的编辑模式
3. 将光标移动到 kernel 那一行，再按一次 e 进入 kernel 该行的编辑模式
4. 在出现的画面当中，最后方输入 single
5. 按下[Enter]确定后，按下 b 就可以开机进入单人维护模式了
6. 现在就登录系统了，此时就可以修改密码了。

## 四、创建，修改和删除

添加、修改和删除用户和用户组可以通过多种方式来完成。其一，可以通过
用户添加、修改、删除等实用命令来完成；其二，还能直接修改与用户和用户组
相关的配置文件来达到目的。第一种方法比较直观但是效率较低，第二种方法要
求对用户和用户组有较深入的了解但也相对效率较高，尤其是在批量添加配置用
户的时候。

不论用命令还是其他第三方程序对用户和用户组进行添加、修改、删
除最终结果还是体现在系统用户配置文件 /etc/passwd 和其对应影
子文件 /etc/shadow 以及用户组配置文件 /etc/group 和其对应影
子文件/etc/gshadow 的改变上。

如何在 Linux 系统中新增一个用户呢？考虑到登录系统时需要输入的信息仅
包括： (1)用户名 (2)密码， 所以建立一个可用的用户最少只也需要这两样数据
就可以了。指定用户名可以使用 useradd 命令，密码设置使用 passwd 这个命
令。

**useradd [选项] 用户名**

1.作用
useradd命令用来建立用户帐号和创建用户的起始目录，使用权限是超级用户。

2.格式
     
    useradd [－d home] [－s shell] [－c comment] [－m [－k template]] 
    [－f inactive] [－e expire ] [－p passwd] [－r] name
     
3.主要参数

    －c：加上备注文字，备注文字保存在passwd的备注栏中。　
    －d：指定用户登入时的启始目录。
    －D：变更预设值。
    －e：指定账号的有效期限，缺省表示永久有效。格式为『YYYY-MM-DD』，指定这个用户失效的日期；
    －f：指定在密码过期后多少天即关闭该账号。0 为立刻失效， -1 为永远不会失效(密码只会过期而强制在登入时重新指定而已) 
    －g：指定用户所属的群组。
    －G：指定用户所属的附加群组。
    －m：自动建立用户的登入目录。（普通用户默认值) ；
    －M：不要自动建立用户的登入目录。(虚拟用户默认值) ；
    －n：取消建立以用户名称为名的群组。
    －r：建立系统账号。
    －s：指定用户登入后所使用的shell。
    －u：指定用户ID号。指定一个特定的 UID 给这个用户，该值必须唯一，不可相同，数值不可为负；
    
4.说明

useradd可用来建立用户账号，它和adduser命令是相同的。账号建好之后，再用passwd设定
账号的密码。使用useradd命令所建立的账号，实际上是保存在/etc/passwd文本文件中。
     
5.应用实例

建立一个新用户账户，并设置ID：
＃useradd caojh －u 544
需要说明的是，设定ID值时尽量要大于500，以免冲突。因为Linux安装后会建立一些特殊用户，
一般0到499之间的值留给bin、mail这样的系统账号。
     
    实例分析
    su（切换到管理员权限）
    useradd –m sam （-m创建用户登录目录）
    passwd sam  （设定密码，在管理员下可以任意修改密码)
    userdel sam  (删掉用户)

**passwd**

<blockquote>养成一个定期修改密码，同时密码可为目前最想做的事情是一个不错的习惯。passwd这个命令就起作用了。</blockquote>
    
    qqin@lizard:[primer_probe_check]$passwd --help
     Usage: passwd [OPTION...] 
     -k, --keep-tokens keep non-expired authentication tokens
     -d, --delete delete the password for the named account (root only)
     -l, --lock lock the password for the named account (root only)
     -u, --unlock unlock the password for the named account (root only)
     -e, --expire expire the password for the named account (root only)
     -f, --force force operation
     -x, --maximum=DAYS maximum password lifetime (root only)
     -n, --minimum=DAYS minimum password lifetime (root only)
     -w, --warning=DAYS number of days warning users receives before password expiration (root only)
     -i, --inactive=DAYS number of days after password expiration when an account becomes disabled (root only)
     -S, --status report password status on the named account (root only)
     --stdin read new tokens from stdin (root only)


如何修改linux用户密码也不是件容易的事,其实非常简单,下面举例说明:
如果是以root身份登录,修改root密码.只要输入
    
    passwd
    
就会出现:
    
    New password:
     Retype new password:

按提示输入密码确认即可.
如果想更改其他用户密码,只要输入
    
    passwd username

即可.如:
    
    passwd kook
     New password:
     Retype new password:


useradd命令增加时，还需使用passwd命令为每一位新增加的用户设置口令；用户以后还可以随时用passwd命令改变自己的口令。
该命令的一般格式为： passwd [用户名] 其中用户名为需要修改口令的用户名。**只有超级用户可以使用“passwd 用户名”修改其他用户的口令，普通用户只能用不带参数的passwd命令修改自己的口令**。

提示自己修改密码（需要管理员权限）：

针对时间的设置，小编只做一个演示，命令 **passwd 用户名 -w 天数** 功能就是修改指定用户的密码更改警告时间，需要注意的是这里的日期单位是天数，如果修改完成，系统会给出提示。其它时间设置的方法与上述方法相同。


**usermod [选项] 用户名**

修改当前系统里存在的某个用户的用户设置。
选项：

     其中-c /-d /-e /-f /-g /-G /-s /-u 选项：与 groupadd 同名选项意义类同 ；
     -l ：后面跟用户名，修改用户名称；
     -L ：暂时将用户锁住，让他无法登录；
     -U ：解锁用户。

usermod 不允许改变正在线上的用户名和用户 UID。当 usermod用来改变用户名和 UID, 必须确认这名用户没在系统上执行任何程序。

**userdel [选项] 用户名**

删除当前系统里某个用户。
选项：

 -r：表示在删除用户的同时，一并把用户的家目录及本地邮件存储的目录或文件也一同删除。

请不要轻易用-r 参数；他会删除用户的同时删除用户所有的文件和目录，切记先删除用户，在确认后再手动删除用户目录；
不允许删除正在线上的用户。

## 五、创建用户组

比添加用户更简单，添加用户组最少只需要指定用户组的名称即可，用groupadd 添加用户组。

groupadd [选项] 用户组名

为系统添加一个新用户组。

选项：

     -g ：后面接一个数字 ，用来给用户组指定一个 GID，该值必须唯一，
    不可相同，数值不可为负；
     -n ：修改组的名称。
     -p ：修改组的密码。

groupmod [选项] 用户组名

为系统添加一个新用户组。

选项：

     -g ：后面接一个数字 ，修改组的 GID；
     -r ：建立一个系统虚拟用户组。

groupdel 用户名

删除当前系统里某个用户组。

*在删除用户组的时候请万分小心，因为这有可能涉及到其它和这个用户组相关的用户；
* 你无法删除一个用户组，除非没有任何一个用户将这个用户作为主用户组(primary group)，也即前面所提到的初始用户组(initialgroup)。

## 六、让渡用户权限

在很多情况下，在多人共管主机，某些用户请求执行某些需要 root 权限的命令或者干脆就是你忙不过来的时候，你就得让其他人获得 root 权限了。但将
系统的 root 密码告诉很多人显然不是一个好的选择，这往往成为悲剧事件的开端。那么我们该如何做呢，sudo 命令给我们提供了一个不需要 root 密码也能执
行 root 权限命令的途径。

sudo 命令允许规定的用户能够以在许可范围内以其他用户身份执行规定的命令。所以，通过 sudo 命令系统管理员可以分配给普通用户一些合理的“权利”，
让他们执行一些只有 root 或其他特许用户才能完成的任务，比如：运行一些像mount，halt，shutdown 之类的命令，或者编辑一些系统配置文件，像/etc/mtab，
/etc/samba/smb.conf 等。这样以来，就不仅减少了 root 用户的登陆次数和管理时间，也提高了系统安全性。sudo 命令有以下特点： 

* sudo 命令能够限制指定用户在指定主机上运行某些命令。
* sudo 命令可以提供日志，忠实地记录每个用户使用 sudo 命令做了些什么，并且能将日志传到中心主机或者日志服务器。
* sudo 命令为系统管理员提供配置文件，允许系统管理员集中地管理用户的使用权限和使用的主机。它默认的存放位置是/etc/sudoers。
* sudo 命令使用时间戳文件来完成类似“检票”的系统。当用户执行 sudo 命令并且输入密码后，用户获得了一张默认存活期为 5 分钟的“入场券”（默认值
可以在编译的时候改变）。超时以后，用户必须重新输入密码。 

**修改/etc/sudoers 文件**
一开始系统默认仅有 root 可以执行 sudo 命令，我们需要通过修改/etc/sudoers 文件来让别的用户也能够执行 sudo 命令。

用 vi /etc/sudoers 打开/etc/sudoers 文件，在没有改动的情况下文件大约有 100 多行，在其中找到一行，如下所示：

    ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL #找到这一行
    intern01 ALL=(ALL) ALL #添加这行
    #表示 intern01 用户可以在任何地方（远程终端）登录，以任意用户身份执行任何命令
    #第一列代表可使用sudo命令的用户名，这里是root ; 第二个字段表示登陆用户的来源主机名称，等号后面是可让渡权限的用户列表；第三列是用户可使用的命令列表

也可以只给部分权限：

    ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL #找到这一行
    intern01 localhost=(root) /sbin/shutdown -h -5
    #表示 intern01 用户可以在本地登录，以 root 用户身份执行/sbin/shutdown -5 now 这个命
    令，命令得以绝对路径的方式指定

这样一个一个用户指定太过麻烦，也可以按用户组指定：

    ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL #找到这一行
    %group_p 192.168.56.130=(root) /sbin/shutdown -h -5
    #表示从属于 group_p 用户组的 用户可以在 192.168.56.130 主机上登录，以 root 用户身
    份执行/sbin/shutdown -5 now 这个命令

事实上，我们甚至可以免除用户输入自己密码的步骤：

    ## Allow root to run any commands anywhere
    root ALL=(ALL) ALL #找到这一行
    %group_p 192.168.56.130=(root) NOPASSWD:/sbin/shutdown -h -5
    #表示从属于 group_p 用户组的 用户可以在 192.168.56.130 主机上登录，以 root 用户身
    份执行/sbin/shutdown -5 now 这个命令,并且无需输入用户密码

* 虽然说如果 sudo 命令允许规定的用户能够以在许可范围内以其他用
户身份执行规定的命令，但是它最常还是用在让普通用户能够以 root
身份执行一些命令;
* sudo 命令不带-u 选项，默认为切换到 root 用户身份，也即 “sudo
fdisk ” 等价于“su –u root fdisk” 

## 七、用户配置文件

配置文件：

* 修改用户（组）
* 添加用户（组）
* 删除用户（组）

### 1.用户相关配置文件

**A）/etc/passwd 文件是什么**

/etc/passwd 是系统识别用户的一个文件，做个不恰当的比喻，
/etc/passwd 是一个花名册，系统所有的用户都在这里有登录记载；

当我们以intern01 这个账号登录时，系统首先会查阅 /etc/passwd 文件，看是否有
intern01 这个账号，然后确定 intern01 的 UID，通过 UID 来确认用户和身
份，如果存在则读取/etc/shadow 影子文件中所对应的 intern01 的密码；如果
密码核实无误则登录系统，读取用户的配置文件；

在/etc/passwd 中，每一行都表示的是一个用户的信息；一行有 7 个字
段；每个字段用:号分割，比如下面是我的系统中的/etc/passwd 的两行；

    intern01:x:500:500:张三:/home/intern01:/bin/bash
    intern02:x:501:502::/home/intern02:/bin/bash

* 第一字段：用户名，在上面的例子中，我们看到这两个用户的用户名分别是 intern01 和 intern02；
* 第二字段：密码；在例子中我们看到的是一个 x，其实密码已被映射到/etc/shadow 文件中；
* 第三字段：UID ；请参看本文的 UID 的解说；
* 第四字段：GID；请参看本文的 GID 的解说；
* 第五字段：用户名全称，这是可选的，可以不设置，在 intern01 这个用户中，用户的全称是张三 ；而 intern02 这个用户没有全称；
* 第六字段：用户的家目录所在位置；intern01 这个用户是/home/intern01 ，而 intern02 这个用户是/home/ intern02 ;
* 第七字段：用户所用 SHELL 的类型，intern01 和 intern02 都用的是bash ；所以为/bin/bash 。

**B）/etc/shadow 文件**

/etc/shadow 文件是/etc/passwd 的影子文件，这个文件并不由
/etc/passwd 而产生的，这两个文件是应该是对应互补的；shadow 内容包括
用户及被加密的密码以及其它/etc/passwd 不能包括的信息，比如用户的有效
期限等；这个文件只有 root 权限可以读取和操作，权限如下：

    -r-------- 1 root root 1.5K 10 月 16 09:49 /etc/shadow

/etc/shadow 的权限不能随便改为其它用户可读，这样做是危险的。如果
你发现这个文件的权限变成了其它用户组或用户可读了，要进行检查，以防系
统安全问题的发生；如果我们以普通用户查看这个文件时，应该什么也查看不
到，提示是权限不够：

    [intern01@localhost ~]$ more /etc/shadow
    /etc/shadow: 权限不够


/etc/shadow 文件的内容包括 9 个段位，每个段位之间用:号分割；我们以
如下的例子说明。

    intern01:$1$VE.Mq2Xf$2c9Qi7EQ9JP8GKF8gH7PB1:16662:0:99999:7:::
    intern02:$1$IPDvUhXP$8R6J/VtPXvLyXxhLWPrnt/:16662:0:99999:7::16668:

* 第一字段：用户名（也被称之为登录名），在例子中有两条记录，也表示有两个用户 intern01 和 intern02
* 第二字段：被加密的密码，如果有的用户在此字段中是 x，表示这个用户不能登录系统，也可以看作是虚拟用户，不过虚拟用户和真实用户都是相对
的，系统管理员随时可以对任何用户操作；
* 第三字段：表示上次更改口令的天数（距 1970 年 01 月 01 日），上面的例子能说明 intern01 和 intern02 这两个用户，是在同一天更改了用户密
码，当然是通过 passwd 命令来更改的，更改密码的时间距 1970 年 01 月
01 日的天数为 13072；
* 第四字段：禁用两次口令修改之间最小天数的功能，设置为 0
* 第五字段：两次修改口令间隔最多的天数，在例子中都是 99999 天；这个值如果在添加用户时没有指定的话，是通过/etc/login.defs 来获取默认
值，PASS_MAX_DAYS 99999；你可以查看/etc/login.defs 来查看，具体的值；
* 第六字段：提前多少天警告用户口令将过期；当用户登录系统后，系统登录程序提醒用户口令将要作废；如果是系统默认值，是在添加用户时由
/etc/login.defs 文件定义中获取，在 PASS_WARN_AGE 中定义；在例子中的值是 7 ，表示在用户口令将过期的前 7 天警告用户更改期口令；
* 第七字段：在口令过期之后多少天禁用此用户；此字段表示用户口令作废多少天后，系统会禁用此用户，也就是说系统会不能再让此用户登录，也
不会提示用户过期，是完全禁用；在例子中，此字段两个用户的都是空的，表示禁用这个功能；
* 第八字段：用户过期日期；此字段指定了用户作废的天数（从 1970 年的 1月 1 日开始的天数），如果这个字段的值为空，帐号永久可用；在例子中，我们看到 intern01 这个用户在此字段是空的，表示此用户永久可用；而 intern02 这个用户表示在距 1970 年 01 月 01 日后 16668 天后过期，算起来也就是 2010 年 11 月 21 号过期
* 第九字段：保留字段，目前为空，以备将来 Linux 发展之用；

### 2. 用户组相关配置文件

**A）/etc/group 文件**

/etc/group 文件是用户组的配置文件，内容包括用户和用户组，并且能显
示出用户是归属哪个用户组或哪几个用户组，因为一个用户可以归属一个或多
个不同的用户组；同一用户组的用户之间具有相似的特征。比如我们把某一用
户加入到 root 用户组，那么这个用户就可以浏览 root 用户家目录的文件，如果
root 用户把某个文件的组读写执行权限开放，root 用户组的所有用户都可以修
改此文件，如果是可执行的文件（比如脚本），root 用户组的用户也是可以执
行的；
用户组的特性在系统管理中为系统管理员提供了极大的方便，但安全性也
是值得关注的，如某个用户下有对系统管理有最重要的内容，最好让用户拥有
独立的用户组，或者是把用户下的文件的权限设置为完全私有；另外 root 用户
组一般不要轻易把普通用户加入进去。

/etc/group 的内容包括用户组（Group）、用户组口令、GID 及该用户组
所包含的用户（User），每个用户组一条记录；格式如下：

    group_name:passwd:GID:user_list

在/etc/group 中的每条记录分四个字段：

* 第一字段：用户组名称；
* 第二字段：用户组密码；
* 第三字段：GID
* 第四字段：用户列表，每个用户之间用,号分割；本字段可以为空；如果字段为空表示用户组为 GID 的用户名；

我们举个例子:

    root:x:0:root,intern02
    #用户组 root，x 是密码段，表示没有设置密码，GID 是 0,root 用户组下包括 root、intern02
    以及 GID 为 0 的其它用户（可以通过/etc/passwd 查看）
    intern01:x:500:intern02
    #用户组 intern01，x 是密码段，表示没有设置密码，GID 是 500,intern01 用户组下包括
    intern02 用户及 GID 为 500 的用户（可以通过/etc/passwd 查看）
    intern02:x:502:intern02
    #用户组 intern02，x 是密码段，表示没有设置密码，GID 是 502,intern02 用户组下包用户
    intern02 及 GID 为 502 的用户（可以通过/etc/passwd 查看）
    helloer:x:503:
    #用户组 helloer，x 是密码段，表示没有设置密码，GID 是 503,helloer 用户组下包括 GID
    为 503 的用户，可以通过/etc/passwd 查看    

而/etc/passwd 对应的相关的记录为：

    root:x:0:0:root:/root:/bin/bash
    intern01:x:500:500:intern01 sun:/home/intern01:/bin/bash
    intern02:x:505:502:intern02 open,intern02 office,13898667715:/home/intern02:/bin/bash
    helloer:x:502:503::/home/helloer:/bin/bash

由此可以看出 helloer 用户组包括 helloer 用户；所以我们查看一个用户组
所拥有的用户，可以通过对比/etc/passwd 和/etc/group 来得到。

**B）/etc/gshadow 文件**

/etc/gshadow 是/etc/group 的加密文件，比如用户组（Group）管理密码
就是存放在这个文件。/etc/gshadow 和/etc/group 是互补的两个文件；对于
大型服务器，针对很多用户和组，定制一些关系结构比较复杂的权限模型，设
置用户组密码是极有必要的。比如我们不想让一些非用户组成员永久拥有用户
组的权限和特性，这时我们可以通过密码验证的方式来让某些用户临时拥有一
些用户组特性，这时就要用到用户组密码。

/etc/gshadow 格式如下，每个用户组独占一行，我们举例来说明：

    intern01:!::intern02
    intern02:oUS/q7NH75RhQ::intern02

* 第一字段：用户组名
* 第二字段：用户组的密码，intern01 用户组无密码；intern02 用户组有已经，已经加密；
* 第三字段：用户组管理者，两者都为空；
* 第四字段：intern01 用户组所拥有的成员是 intern02 ，然后还要对照一下
/etc/group 和/etc/passwd 查看是否还有其它用户，一般默认添加的用
户，有时同时也会创建用户组和用户名同名称； intern02 用户组有成员
intern02。


## 简单的案例

1.新建phpq用户

    adduser phpq
    passwd phpq //给phpq用户设置密码

<!-- more -->
2、建工作组
    
    groupadd test //新建test工作组

3、新建用户同时增加工作组
    
    useradd -g test phpq //新建phpq用户并增加到test工作组

注：：-g 所属组 -d 家目录 -s 所用的SHELL

4、给已有的用户增加工作组
    
    usermod -G groupname username

或者：gpasswd -a user group

5、临时关闭：在/etc/shadow文件中属于该用户的行的第二个字段（密码）前面加上*就可以了。想恢复该用户，去掉*即可。

或者使用如下命令关闭用户账号：
    
    passwd peter –l

重新释放：
    
    passwd peter –u

6、永久性删除用户账号
    
    userdel peter
    groupdel peter
    usermod –G peter peter （强制删除该用户的主目录和主目录下的所有文件和子目录）

7、从组中删除用户

编辑/etc/group 找到GROUP1那一行，删除 A
或者用命令

    gpasswd -d A GROUP

8、显示用户信息
    
    id user
    cat /etc/passwd

**参考资料：**

http://www.cnblogs.com/xd502djj/archive/2011/11/23/2260094.html **

http://yulei568.blog.163.com/blog/static/135886720079592436818/

宁波城市职业技术学院 http://www.icourse163.org/course/NBCC-437004

http://linux.ximizi.com/linux/4/linux30940.htm
