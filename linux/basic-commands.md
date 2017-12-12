#linux的基础知识


## linux 目录

Linux 文件系统包含排列在磁盘或其他区块存储设备的目录中的文件。
与其他许多系统相同，Linux 系统中的目录也包含其他目录，即子目录。 
Microsoft® Windows® 等系统的概念是在不同的驱动器盘符上（A:，C:，等）使用不同的文件系统。
而 Linux 文件系统则截然不同，它是一个树形结构，以 / 目录作为根 目录。

![ Linux 文件系统](http://upload-images.jianshu.io/upload_images/3739049-afbfb3251f54f472.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


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



## 绝对路径与相对路径

绝对路径：路径的写法『一定由根目录/ 写起』，例如： /usr/share/doc 这个目录。

相对路径：路径的写法『不是由 / 写起』，例如由/usr/share/doc 要到 /usr/share/man 底下时，可以写成： 『cd ../man』这就是相对路径的写法啦！相对路径意指『相对于目前工作目录的路径！

     .         代表此层目录
     ..        代表上一层目录
     -         代表前一个工作目录
     ~         代表『目前用户身份』所在的家目录

~account  代表 account 这个用户的家目录(account是个账号名称)

    cd：变换目录
    pwd：显示当前目录
    mkdir：建立一个新的目录
    rmdir：删除一个空的目录


## ls命令

    [root@www ~]# ls [-aAdfFhilnrRSt] 目录名称
    [root@www ~]# ls [--color={never,auto,always}] 目录名称
    [root@www ~]# ls [--full-time] 目录名称

    选项与参数：
     -a ：全部的档案，连同隐藏档( 开头为 . 的档案) 一起列出来(常用)
     -A ：全部的档案，连同隐藏档，但不包括 . 与 .. 这两个目录
     -d ：仅列出目录本身，而不是列出目录内的档案数据(常用)
     -f ：直接列出结果，而不进行排序 (ls 预设会以档名排序！)
     -F ：根据档案、目录等信息，给予附加数据结构，例如：
     *:代表可执行文件； /:代表目录； =:代表 socket 档案； |:代表 FIFO 档案；
     -h ：将档案容量以人类较易读的方式(例如 GB, KB 等等)列出来；
     -i ：列出 inode 号码，inode 的意义下一章将会介绍；
     -l ：长数据串行出，包含档案的属性与权限等等数据；(常用)
     -n ：列出 UID 与 GID 而非使用者与群组的名称(UID与GID会在账号管理提到！)
     -r ：将排序结果反向输出，例如：原本档名由小到大，反向则为由大到小；
     -R ：连同子目录内容一起列出来，等于该目录下的所有档案都会显示出来；
     -S ：以档案容量大小排序，而不是用档名排序；
     -t ：依时间排序，而不是用档名。
     --color=never ：不要依据档案特性给予颜色显示；<strong>默认的有执行权限的为绿色字体，文件夹为绿色背景</strong>
     --color=always ：显示颜色
     --color=auto ：让系统自行依据设定来判断是否给予颜色
     --full-time ：以完整时间模式 (包含年、月、日、时、分) 输出
     --time={atime,ctime} ：输出 access 时间或改变权限属性时间(ctime)
     而非内容变更时间(modification time)
     只有下达 ls 时，默认显示的只有：非隐藏档的档名、以档名进行排序及文件名代表的颜色显示如此而已

## cp命令

    [root@www ~]# cp [-adfilprsu] 来源文件(source) 目标文件(destination)
    [root@www ~]# cp [options] source1 source2 source3 .... directory

    
    选项与参数：
     -a ：相当于 -pdr 的意思，至于 pdr 请参考下列说明；(常用)
     -d ：若来源文件为链接文件的属性(link file)，则复制链接文件属性而非档案本身；
     -f ：为强制(force)的意思，若目标档案已经存在且无法开启，则移除后再尝试一次；
     -i ：若目标文件(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
     -l ：进行硬式连结(hard link)的连结档建立，而非复制档案本身；
     -p ：连同档案的属性一起复制过去，而非使用默认属性(备份常用)；
     -r ：递归持续复制，用于目录的复制行为；(常用)
     -s ：复制成为符号链接文件(symbolic link)，亦即
     档案；
     -u ：若destination 比 source 旧才更新 destination ！
     最后需要注意的，如果来源档有两个以上，则最后一个目标文档一定要是『目录』才行！

## rm命令

    [root@www ~]# rm [-fir] 档案或目录
    
    选项与参数：
     -f ：就是 force 的意思，忽略不存在的档案，不会出现警告讯息；
     -i ：互动模式，在删除前会询问使用者是否动作
     -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！

范例一：将刚刚在 cp 的范例中建立的 bashrc 删除掉！

    [root@www ~]# cd /tmp
    [root@www tmp]# rm -i bashrc
    rm: remove regular file `bashrc'? y

如果加上 -i 的选项就会主动询问喔，避免你删除到错误的档名！

范例二：透过通配符*的帮忙，将/tmp底下开头为bashrc的档名通通删除：

    [root@www tmp]# rm -i bashrc*

注意那个星号，代表的是 0 到无穷多个任意字符喔！很好用的东西！

范例三：将 cp 范例中所建立的 /tmp/etc/ 这个目录删除掉！

    [root@www tmp]# rmdir /tmp/etc
    rmdir: etc: Directory not empty <== 删不掉啊！因为这不是空的目录！
    [root@www tmp]# rm -r /tmp/etc
    rm: descend into directory `/tmp/etc'? y
    ....(中间省略)....

因为身份是 root ，预设已经加入了 -i 的选项，所以你要一直按 y 才会删除！
如果不想要继续按 y ，可以按下『[ctrl]-c 』来结束 rm 的工作。
这是一种保护的动作，如果确定要删除掉此目录而不要询问，可以这样做：

    [root@www tmp]# \rm -r /tmp/etc

在指令前加上反斜杠，可以忽略掉 alias 的指定选项喔！至于 alias 我们在bash再谈！

范例四：删除一个带有 - 开头的档案

    [root@www tmp]# touch ./-aaa- <==touch这个指令可以建立空档案！
    [root@www tmp]# ls -l
    -rw-r--r-- 1 root root 0 Sep 24 15:03 -aaa- <==档案大小为0，所以是空档案
    [root@www tmp]# rm -aaa-
    Try `rm --help' for more information. <== 因为 "-" 是选项嘛！所以系统误判了！
    [root@www tmp]# rm ./-aaa-

## mv命令

    [root@www ~]# mv [-fiu] source destination
    [root@www ~]# mv [options] source1 source2 source3 .... directory
    
    选项与参数：
     -f ：force 强制的意思，如果目标档案已经存在，不会询问而直接覆盖；
     -i ：若目标档案(destination) 已经存在时，就会询问是否覆盖！
     -u ：若目标档案已经存在，且source 比较新，才会更新 (update)

范例一：复制一档案，建立一目录，将档案移动到目录中

    [root@www ~]# cd /tmp
    [root@www tmp]# cp ~/.bashrc bashrc
    [root@www tmp]# mkdir mvtest
    [root@www tmp]# mv bashrc mvtest

将某个档案移动到某个目录去，就是这样做！

范例二：将刚刚的目录名称更名为 mvtest2

    [root@www tmp]# mv mvtest mvtest2 <== 这样就更名了！简单～

其实在 Linux 底下还有个有趣的指令，名称为 rename ，
该指令专职进行多个档名的同时更名，并非针对单一档名变更，与mv不同。请man rename。

范例三：再建立两个档案，再全部移动到 /tmp/mvtest2 当中
    
    [root@www tmp]# cp ~/.bashrc bashrc1
    [root@www tmp]# cp ~/.bashrc bashrc2
    [root@www tmp]# mv bashrc1 bashrc2 mvtest2

注意到这边，如果有多个来源档案或目录，则最后一个目标文件一定是『目录！』
意思是说，将所有的数据移动到该目录的意思！

*取得路径的文件名与目录名称*

    [root@www ~]# basename /etc/sysconfig/network
    network <== 很簡單！就取得最後的檔名～
    [root@www ~]# dirname /etc/sysconfig/network
    /etc/sysconfig <== 取得的變成目錄名了！

文件处理过程免不了需要压缩和解压缩，这里整理一下常见的压缩相关的命令
    
    1、*.tar 用 tar –xvf 解压
    2、*.gz 用 gzip -d或者gunzip 解压
    3、*.tar.gz和*.tgz 用 tar –xzf 解压
    4、*.bz2 用 bzip2 -d或者用bunzip2 解压
    5、*.tar.bz2用tar –xjf 解压
    6、*.Z 用 uncompress 解压
    7、*.tar.Z 用tar –xZf 解压
    8、*.rar 用 unrar e解压
    9、*.zip 用 unzip 解压
    

## 链接 

Linux链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。

默认情况下，ln命令产生硬链接。
 
####【硬连接】

硬连接指通过索引节点来进行连接。在Linux的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在Linux中，多个文件名指向同一索引节点是存在的。一般这种连接就是硬连接。硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

####【软连接】

另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于Windows的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

通过实验加深理解
    
    [oracle@Linux]$ touch f1          #创建一个测试文件f1
     [oracle@Linux]$ ln f1 f2          #创建f1的一个硬连接文件f2
     [oracle@Linux]$ ln -s f1 f3       #创建f1的一个符号连接文件f3
     [oracle@Linux]$ ls -li            # -i参数显示文件的inode节点信息
     total 0
     9797648 -rw-r--r--  2 oracle oinstall 0 Apr 21 08:11 f1
     9797648 -rw-r--r--  2 oracle oinstall 0 Apr 21 08:11 f2
     9797649 lrwxrwxrwx  1 oracle oinstall 2 Apr 21 08:11 f3 -> f1

从上面的结果中可以看出，硬连接文件f2与原文件f1的inode节点相同，均为9797648，然而符号连接文件的inode节点不同。
    
    [oracle@Linux]$ echo "I am f1 file" >>f1
     [oracle@Linux]$ cat f1
     I am f1 file
     [oracle@Linux]$ cat f2
     I am f1 file
     [oracle@Linux]$ cat f3
     I am f1 file
     [oracle@Linux]$ rm -f f1
     [oracle@Linux]$ cat f2
     I am f1 file
     [oracle@Linux]$ cat f3
     cat: f3: No such file or directory


通过上面的测试可以看出：当删除原始文件f1后，硬连接f2不受影响，但是符号连接f1文件无效

#### 总结


依此您可以做一些相关的测试，可以得到以下全部结论：

    1).删除符号连接f3,对f1,f2无影响；
    2).删除硬连接f2，对f1,f3也无影响；
    3).删除原文件f1，对硬连接f2没有影响，导致符号连接f3失效；
    4).同时删除原文件f1,硬连接f2，整个文件会真正的被删除

PS:

1. 强烈不建议给目录创建硬链接，容易造成目录链接死循环
2. 不能跨硬盘分区创建硬链接

## 通配符

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

#### 例子

1.拷贝文件

    cp /tmp/file[0-9].txt -

2.删除a1或a2开头的文件夹

    rm -fr {a1,a2}*.sh


### 本章节作者：铁汉1990


