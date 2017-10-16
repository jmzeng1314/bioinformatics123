---
author: sam
comments: true
date: 2017-07-07 01:54:20+00:00
#layout: post
link: http://hugo.qinqianshan.com/linux-setfacl
slug: linux-setfacl
title: Linux【2】-管理文件20-给你的文件再次加权限(setfacl)
wordpress_id: 774
categories:
- basic_description
tags:
- getfcal
- setfacl
---

<blockquote>       往服务器里塞一个文件的时候，怎么都拽不进去，查看权限也都是有的，唯一的奇怪地方就是在rwxrwxrwx+多了一个+，究竟这个+是不是罪魁祸首，而他又是从哪来的。这一切还得从**setfacl**说起，真相这就揭晓。</blockquote>


<!-- more -->


## 一、基本规则


**1.1基本参数**

	
	-b,--remove-all：删除所有扩展的acl规则，基本的acl规则(所有者，群组，其他）将被保留。 
	-k,--remove-default：删除缺省的acl规则。如果没有缺省规则，将不提示。
	 -n，--no-mask：不要重新计算有效权限。setfacl默认会重新计算ACL mask，除非mask被明确的制定。
	 --mask：重新计算有效权限，即使ACL mask被明确指定。
	 -d，--default：设定默认的acl规则。
	 --restore=file：从文件恢复备份的acl规则（这些文件可由getfacl -R产生）。通过这种机制可以恢复整个目录树的acl规则。此参数不能和除--test以外的任何参数一同执行。
	 --test：测试模式，不会改变任何文件的acl规则，操作后的acl规格将被列出。
	 -R，--recursive：递归的对所有文件及目录进行操作。
	 -L，--logical：跟踪符号链接，默认情况下只跟踪符号链接文件，跳过符号链接目录。
	 -P，--physical：跳过所有符号链接，包括符号链接文件。
	 --version：输出setfacl的版本号并退出。
	 --help：输出帮助信息。
	 --：标识命令行参数结束，其后的所有参数都将被认为是文件名
	 -：如果文件名是-，则setfacl将从标准输入读取文件名。


选项-m和-x后边跟以acl规则。多条acl规则以逗号(,)隔开。选项-M和-X用来从文件或标准输入读取acl规则。 选项--set和--set-file用来设置文件或目录的acl规则，先前的设定将被覆盖。 选项-m(--modify)和-M(--modify-file)选项修改文件或目录的acl规则。 选项-x(--remove)和-X(--remove-file)选项删除acl规则。

当使用-M，-X选项从文件中读取规则时，setfacl接受getfacl命令输出的格式。每行至少一条规则，以#开始的行将被视为注释。 当在不支持ACLs的文件系统上使用setfacl命令时，setfacl将修改文件权限位。如果acl规则并不完全匹配文件权限位，setfacl将会修改文件权限位使其尽可能的反应acl规则，并会向standard error发送错误消息，以大于0的状态返回。

**1.2 权限**

文件的所有者以及有CAP_FOWNER的用户进程可以设置一个文件的acl。（在目前的linux系统上，root用户是唯一有CAP_FOWNER能力的用户）

**1.3 ACL规则**

	
	[d[efault]:] [u[ser]:]uid [:perms] 指定用户的权限，文件所有者的权限（如果uid没有指定） 
	[d[efault]:] g[roup]:gid [:perms] 指定群组的权限，文件所有群组的权限（如果gid未指定） 
	[d[efault]:] m[ask][:] [:perms] 有效权限掩码 [d[efault]:] o[ther] [:perms] 其他的权限


恰当的acl规则被用在修改和设定的操作中，对于uid和gid，可以指定一个数字，也可指定一个名字。perms域是一个代表各种权限的字母的组合：读-r写-w执行-x，执行只适合目录和一些可执行的文件。pers域也可设置为八进制格式。


## 二、 ACL的名词定义


ACL是由一系列的Access Entry所组成的，每一条Access Entry定义了特定的类别可以对文件拥有的操作权限。Access Entry有三个组成部分：Entry tag type, qualifier (optional), permission。

	
	ACL_USER_OBJ：相当于Linux里file_owner的permission
	 ACL_USER：定义了额外的用户可以对此文件拥有的permission
	 ACL_GROUP_OBJ：相当于Linux里group的permission
	 ACL_GROUP：定义了额外的组可以对此文件拥有的permission
	 ACL_MASK：定义了ACL_USER, ACL_GROUP_OBJ和ACL_GROUP的最大权限 (这个我下面还会专门讨论)
	 ACL_OTHER：相当于Linux里other的permission



	
	[root@localhost ~]# getfacl ./test.txt
	 # file: test.txt
	 # owner: root
	 # group: admin
	 user::rw-
	 user:john:rw-
	 group::rw-
	 group:dev:r—
	 mask::rw-
	 other::r--


前面三个以#开头的定义了文件名，file owner和group。这些信息没有太大的作用，接下来我们可以用--omit-header来省略掉。

	
	user::rw- 定义了ACL_USER_OBJ, 说明file owner拥有read and write permission
	user:john:rw- 定义了ACL_USER,这样用户john就拥有了对文件的读写权限,实现了我们一开始要达到的目的
	group::rw- 定义了ACL_GROUP_OBJ,说明文件的group拥有read and write permission group:dev:r-- 定义了ACL_GROUP,使得dev组拥有了对文件的read permission
	mask::rw- 定义了ACL_MASK的权限为read and write
	other::r-- 定义了ACL_OTHER的权限为read




## 三、如何设置ACL文件


如何设置ACL文件 首先我们还是要讲一下设置ACL文件的格式，从上面的例子中我们可以看到每一个Access Entry都是由三个被：号分隔开的字段所组成，第一个就是Entry tag type。

	
	[root@localhost ~]# ls -l
	 -rw-rw-r-- 1 root admin 0 Jul 3 22:06 test.txt
	 [root@localhost ~]# getfacl --omit-header ./test.txt
	 user::rw- group::rw- other::r--


我们先让用户john拥有对test.txt文件的读写权限：

	
	[root@localhost ~]# setfacl -m user:john:rw- ./test.txt
	 [root@localhost ~]# getfacl --omit-header ./test.txt
	 user::rw- user:john:rw-
	 group::rw-
	 mask::rw-
	 other::r--


这时我们就可以看到john用户在ACL里面已经拥有了对文件的读写权。这个时候如果我们查看一下linux的permission我们还会发现一个不一样的地方。

	
	[root@localhost ~]# ls -l ./test.txt
	 -rw-rw-r--+ 1 root admin 0 Jul 3 22:06 ./test.txt


在文件permission的最后多了一个+号，当任何一个文件拥有了ACL_USER或者ACL_GROUP的值以后我们就可以称它为ACL文件，这个+号就是用来提示我们的。我们还可以发现当一个文件拥有了ACL_USER或者ACL_GROUP的值时ACL_MASK同时也会被定义。

	
	接下来我们来设置dev组拥有read permission：
	 [root@localhost ~]# setfacl -m group:dev:r-- ./test.txt
	 [root@localhost ~]# getfacl --omit-header ./test.txt
	 user::rw- user:john:rw-
	 group::rw- group:dev:r—
	 mask::rw-
	 other::r--




## 四、ACL_MASK和Effective permission


这里需要重点讲一下ACL_MASK，因为这是掌握ACL的另一个关键，在Linux file permission里面大家都知道比如对于rw-rw-r--来说, 当中的那个rw-是指文件组的permission. 但是在ACL里面这种情况只是在ACL_MASK不存在的情况下成立。如果文件有ACL_MASK值，那么当中那个rw-代表的就是mask值而不再是group permission了。

	
	[root@localhost ~]# setfacl -m mask::r-- ./test.sh
	 [root@localhost ~]# getfacl --omit-header ./test.sh
	 user::rwx user:john:rwx #effective:r—
	 group::rw- #effective:r-- mask::r-- other::r—


ACL_MASK的定义。它规定了ACL_USER，ACL_GROUP_OBJ和ACL_GROUP的最大权限。
最牛逼的权限，所有权限不能逾越ACL_mask的权限


## 五、Default ACL


Default ACL是指对于一个目录进行Default ACL设置，并且在此目录下建立的文件都将继承此目录的ACL。

	
	 [root@localhost ~]# mkdir dir


他希望所有在此目录下建立的文件都可以被john用户所访问，那么我们就应该对dir目录设置Default ACL。

	
	[root@localhost ~]# setfacl -d -m user:john:rw ./dir
	 [root@localhost ~]# getfacl --omit-header ./dir
	 user::rwx group::rwx
	 other::r-x default:user::rwx
	 default:user:john:rwx
	 default:group::rwx
	 default:mask::rwx
	 default: other::r-x




## 六、支持acl


**6.1 本篇所使用的setfacl版本：**

	
	[root@rhel6-server acltest]# setfacl --version
	 setfacl 2.2.49


**6.2查看文件系统是否支持acl权限控制：**

	
	[root@rhel6-server acltest]# tune2fs -l /dev/sda3 | grep option
	 Default mount options: acl


**6.3开启acl支持有两种方法：**

1）修改mount选项：

	
	mount -o remount,acl /dev/vda3 /mnt/acltest


开机自动挂载：

	
	vim /etc/fstab
	/dev/vda3 /mnt/acltest ext4 defaults,acl 0 0


2）使用tune2fs修改文件系统信息：

	
	tune2fs开启acl后已是永久有效，无需再改fstab的mount选项：
	 tune2fs -o acl /dev/vda3 修改文件系统自身信息来设置acl选项
	 tune2fs -o ^acl /dev/vda3 取消acl选项




## 七、注意的地方


**7.1 chacl删除其他文件**

前面的例子中我们都注意到了getfacl命令是用来读取文件的ACL，setfacl是用来设定文件的Acess ACL。这里还有一个chacl是用来改变文件和目录的Access ACL and Default ACL，它的具体参数大家可以去看man page。我只想提及一下chacl -B。它可以彻底删除文件或者目录的ACL属性(包括Default ACL)，比如你即使用了setfacl -x删除了所有文件的ACL属性，那个+号还是会出现在文件的末尾，所以正确的删除方法应该是用chacl -B用cp来复制文件的时候我们现在可以加上-p选项。这样在拷贝文件的时候也将拷贝文件的ACL属性，对于不能拷贝的ACL属性将给出警告。 mv命令将会默认地移动文件的ACL属性，同样如果操作不允许的情况下会给出警告。

**7.2 与chmod的冲突**

如果用chmod命令改变Linux file permission的时候相应的ACL值也会改变，反之改变ACL的值，相应的file permission也会改变。

对于组权限，setfacl设置的权限只对主组（即useradd -g或usermod -g的组）有效，对附加组（即useradd -G或usermod -aG的组）无效，即使文件的所有组已改为附加组

setfacl和chmod设置的权限可以相互覆盖，当二者设置的权限不一致时，以使用getfacl看到的“#effective:”后的权限为准。

**参考资料：**

http://man.linuxde.net/setfacl （超赞）

http://blog.csdn.net/t0nsha/article/details/7281151

http://www.linuxquestions.org/questions/linux-newbie-8/what-is-in-drwxrwxrwx-915243/

http://www.linux-mag.com/id/7772/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+LinuxMagazine+%28Linux+Magazine%3A+Top+Stories%29&utm_content=Google+Feedfetcher
