---
author: sam
comments: true
date: 2017-06-30 03:22:57+00:00
#layout: post
link: http://hugo.qinqianshan.com/git/
slug: git
title: Linux【4】-文本编辑2-git
wordpress_id: 471
categories:
- basic_description
tags:
- git
---

<blockquote>          这周发生了 一件很诡异的事情，自己回家写了一会代码，结果第二天去公司，发现将近2000多行的代码只有300多行了，这下子把我给吓尿了。幸运的是昨晚下班之前用git备份了一下代码。痛定思痛后，决定好好学习理解一下git。</blockquote>

Git是目前世界上最先进的分布式版本控制系统。牛逼之处就在于可以很多人协同管理文件，同时记录每次的修改。那就来看看究竟怎么个牛逼法吧

<!-- more -->
注：如果你之前对git没有什么概念:
	
* [学习Git的在线互动教程](http://pcottle.github.io/learnGitBranching/)
* [版本管理工具介绍—Git篇](http://www.imooc.com/view/208)
*  [git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
* [廖神的博客](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
* [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)


## 一、安装

打开终端输入 git 查看是否安装
一般都会显示安装了这个工具，如果没有装，请参见[ 这里](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)

## 二、使用

### 1.创建版本库

版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。
所以，创建一个版本库非常简单，首先，选择一个合适的地方，创建一个空目录：
		
	$ mkdir git_copy
	$ cd git_copy
	$ pwd
	/Users/sam/git_copy

### 2.私有化版本库

通过git init命令把这个目录变成Git可以管理的仓库
		
	$ git init
	Initialized empty Git repository in /Users/sam/git_copy/.git/

瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。（如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见。）
也不一定必须在空目录下创建Git仓库，选择一个已经有东西的目录也是可以的。不过，不建议你使用自己正在开发的公司项目来学习Git，否则造成的一切后果概不负责。

所有的版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，Git也不例外。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。Microsoft的Word格式是二进制格式，因此，版本控制系统是没法跟踪Word文件的改动的。千万不要使用Windows自带的记事本编辑任何文本文件。建议你下载Notepad++代替记事本，不但功能强大，而且免费！记得把Notepad++的默认编码设置为UTF-8 without BOM即可。

### 3.备份文件

在git_copy目录下新建一个文本follow。定要放到git_copy目录下（子目录也行），因为这是一个Git仓库，放到其他地方Git再厉害也找不到这个文件。
		
	git addfollow

用命令git commit告诉Git，把文件提交到仓库：
		
	git commit -m "wrote a readme file”
	 1 file changed, 2 insertions(+)
	 create mode 100644 follow
	-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，
	这样你就能从历史记录里方便地找到改动记录

因为commit可以一次提交很多文件，所以你可以多次add不同的文件，比如：
		
	$ git add file1.txt
	$ git add file2.txt file3.txt
	$ git commit -m "add 3 files."

### 4.查看你的修改

在follow文件中加入几句话：
		
	Git is a distributed version control system.
	Git is free software.
		
	git status
	On branch master
	Changes not staged for commit:
	 (use "git add <file>..." to update what will be committed)
	 (use "git checkout -- <file>..." to discard changes in working directory)
	 modified: follow
	no changes added to commit (use "git add" and/or "git commit -a”)

上面的命令告诉我们，readme.txt被修改过了，但还没有准备提交的修改。
		
	git diff follow
	
	diff --git a/follow b/follow
	index a45571a..997128f 100644
	--- a/follow
	+++ b/follow
	@@ -1,2 +1,4 @@
	-12rr
	+DDDi12rr
	 122333
	+Git is a distributed version control system.
	+Git is free software

然后接着git add follow ; git status;git commit -m “ddd” ;git stauts 看一下上传修改后status的一种变化

在git提交环节，存在三大部分：working tree, index file, commit
working tree：就是你所工作在的目录，每当你在代码中进行了修改，working tree的状态就改变了。
index file：是索引文件，它是连接working tree和commit的桥梁，每当我们使用git-add命令来登记后，index file的内容就改变了，此时index file就和working tree同步了。
commit：是最后的阶段，只有commit了，我们的代码才真正进入了git仓库。我们使用git-commit就是将index file里的内容提交到commit中。

总结一下：

git diff：是查看working tree与index file的差别的。
git diff --cached：是查看index file与commit的差别的。
git diff HEAD：是查看working tree和commit的差别的。（你一定没有忘记，HEAD代表的是最近的一次commit的信息）

### 4.找回文件

通过add,commit多次修改上传后，你可以通过
		
	git log

查看最近的修改

git log命令显示从最近到最远的提交日志，我们可以看到3次提交
		
如果嫌输出信息太多，看得眼花缭乱的，可以试试--pretty=oneline参数：

	git log —pretty=oneline

Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
		
	git reset --hard HEAD^
	 HEAD is now at ea34578 add distributed

退回到倒数第二个上传的版本

那退回到倒数第二个版本后，我们是否还能退回到倒数第一个版本呢
只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个的commit id是3628164...，于是就可以指定回到未来的某个版本：
		
	git reset --hard 3628164
	 HEAD is now at 3628164 append GPL

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

Git提供了一个命令
		
	git reflog

用来记录你的每一次命令，这样你就可以又回到你像去得版本了，只要能找到ID
		
	git checkout -- readme.txt

git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

### 5.远程备份

第1步：**创建SSH Key**。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
		
	$ ssh-keygen -t rsa -C "youremail@example.com"

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

第2步：**登陆GitHub**，打开“Account settings”，“SSH Keys”页面：

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：
为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。
注：不交钱，github上的东西是公开的

第3步：登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：
在Repository name填入git_copy，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库：

第四步：我们根据GitHub的提示，在本地的仓库下git_copy运行命令：
		
	$ git remote add origin git@github.com:sam/git_copy.git
	git remote rm origin 如果弄错了，可以通过这样来删除
		
请千万注意，把上面的sam替换成你自己的GitHub账户名，git_copy为你在github创建的库的名字。否则，你在本地关联的就是我的远程库，关联没有问题，但是你以后推送是推不上去的，因为你的SSH Key公钥不在我的账户列表中。
添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

	git push -u origin master

把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。

由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：
		
	git push origin master


把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

**SSH警告：**

当你第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：
		
	The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
	RSA key fingerprint is xx.xx.xx.xx.xx.
	Are you sure you want to continue connecting (yes/no)?

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；

### 6. 从远程库克隆

上次我们讲了先有本地库，后有远程库的时候，如何关联远程库。
现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。

首先，登陆GitHub，创建一个新的仓库，名字叫gitskills：
我们勾选Initialize this repository with a README，这样GitHub会自动为我们创建一个README.md文件。创建完毕后，可以看到README.md文件：
现在，远程库已经准备好了，下一步是用命令git clone克隆一个本地库：

		
	git clone git@github.com:michaelliao/gitskills.git
	 Cloning into 'gitskills'...
	 remote: Counting objects: 3, done.
	 remote: Total 3 (delta 0), reused 0 (delta 0)
	 Receiving objects: 100% (3/3), done.
	
	$ cd gitskills
	 $ ls
	 README.md

注意把Git库的地址换成你自己的，然后进入gitskills目录看看，已经有README.md文件了。
如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了。

你也许还注意到，GitHub给出的地址不止一个，还可以用https://github.com/michaelliao/gitskills.git这样的地址。实际上，Git支持多种协议，默认的git://使用ssh，但也可以使用https等其他协议。
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

### 6.分支管理

查看分支，当前分支是为*标记
		
	qqin@gecko:[snp_primer]$<strong>git branch</strong>
	* dev
	 master

切换到master分支上
		
	qqin@gecko:[snp_primer]$<strong>git checkout master</strong>
	qqin@gecko:[snp_primer]$git branch
	 dev
	* master

创建名字为name的新的分支
		
	git brance name 
		
合并分支类容
		
	git merge 用于合并指定分支的内容到当前分支上
	git merge dev 则是将分支dev上的内容合并到当前分支上来

删除分支
		
	git branch –d dev 删除dev分支

删除远程分支
		
	git remote remove zzong


推送分支
		
	git remote 查看远程库的信息
	git remote –v 显示更详细的信息
	origin git@dragon:genoprime (fetch)
	origin git@dragon:genoprime (push)

创建远程库
		
	git remote add origin git@dragon:genoweb
	在dragon服务器上的git文件夹下的genoweb的git库建立名为origin的库，成为远程库
	
	git push origin master 把master分支推送到远程分支上
	git push origin dev 将dev分支推送到远程分支上

抓取分支

	git clone git@github.com:sam/learngit.git
	git checkout –b dev origin/dev 创建远程Origin 的dev分支到本地
	
	git pull 从远程下载，合并再推送
	git pull失败后 git branch --set-upstream dev origin/dev，再git pull
			
	git remote add haha  /home/haha/
	git fetch zgong
	git merge zgong/master

### 7.打上Tag

在master分支上打上tag
		
	git tag -a  v0.0.3 -m "supply search by GWV"

### 8.git 配置

	git config --global user.name "xxx" -- 配置用户名，上传本地 repository 到服务器上的时候，在 Github 上会显示这里配置的上传者信息
	 git config --global user.email "xxx" -- 配置邮箱
	 git config --list 查看配置列表

比如，让Git显示颜色，会让命令输出看起来更醒目：
		
	$ git config --global color.ui true

替换命令名字
		
	我们只需要敲一行命令，告诉Git，以后st就表示status：
	$ git config --global alias.st status
	
	git rm xxx -- 从本地仓库中删除指定文件
	git rm -r xxx -- 从本地仓库中删除指定文件夹

使用 .gitignore 文件忽略指定的内容：
		
	 1. 在本地仓库根目录创建 .gitignore 文件(跟.git同级，非.git文件夹下)。git 对于.ignore 配置文件是按行从上到下进行规则匹配的，意味着如果前面的规则匹配的范围更大，则后面的规则将不会生效
	 2. 过滤文件和文件夹：
	 Temp\temp过滤： Temp\temp
	 *.suo 过滤： *.suo
	 3. 不过滤文件和文件夹： !*.c
	
	或者直接在项目文件下
	vim .gitignore
	
	忽略指定文件方法二：
	在git库中
	ls -lhta
	cd .git
	ls -lht
	cd info/
	vim exclude
	文本中添加 *.pyc  这样就忽略pyc文件了

## 三、git管理

现在的项目中有一个开发版本，一个发布版本，需要通过git来同步管理
		
	#rm -fr .git
	#重建 git init
	上传需要git管理的文件
	git add *
	git commit –m "dd"
	忽略上传的文件
	vim .gitignore
	克隆过来
	
	git clone GenoPrime GenoPrime_dev
	cd GenoPrime_dev
	git push 将修改后的内容Push到发布版本中
	cd GenoPrime 
	git merge ** **  (注意是从哪个分支到哪个分支，回头一定要merge一下呀)
		
协作
		
	新开分支
	git checkout -b dev
	切换分支
	git checkout master
	
	查看分支
	git branch –av
	添加新的协作
	git remote add  zgong /home/zgong/wwwwww
	git diff 
	git fetch zgong
	git merge /zgong/dev
		1068  git log
		1069  git reset --hard 8d33c5284707f3bc96b24118b2
		Auto-merging GenoPrimeWeb.py
		CONFLICT (content): Merge conflict in GenoPrimeWeb.py
		Automatic merge failed; fix conflicts and then commit the result.
	vim GenoPrimeWeb.py
	修改完冲突后，get merge **
		
注：

更多命令介绍，请 git 一下看参数介绍
git强大之处已超过我的想象，更多功能在后续实践中再加以补充

## 四、报错与讨论

### 1.解决mac升级10.11后，出现的 xcrun: error: invalid active developer path, missing xcrun 错误

**xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun**

重装了一下xcode command line
		
	xcode-select --install
		
上面那步一运行，就可以git罗
如果不行，就需来切换位置
		
	sudo xcode-select -switch /
		
### 2.fetch 与pull的区别

Git中从远程的分支获取最新的版本到本地有这样2个命令：

1.git fetch：相当于是从远程获取最新版本到本地，不会自动merge

	git fetch origin master
	git log -p master..origin/master
	git merge origin/master

以上命令的含义：

首先从远程的origin的master主分支下载最新的版本到origin/master分支上
然后比较本地的master分支和origin/master分支的差别
最后进行合并
上述过程其实可以用以下更清晰的方式来进行：

	git fetch origin master:tmp
	git diff tmp 
	git merge tmp

从远程获取最新的版本到本地的test分支上
之后再进行比较合并

2.git pull：相当于是从远程获取最新版本并merge到本地

	git pull origin master

上述命令其实相当于git fetch 和 git merge
在实际使用中，git fetch更安全一些
因为在merge前，我们可以查看更新情况，然后再决定是否合并


## 参考资料：

http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000

diff的区别：http://blog.sina.com.cn/s/blog_40e9d4dd0100xi8p.html

http://www.cnblogs.com/zuibunan/p/3843241.html

http://elfxp.com/mac-xcrun-error/

http://blog.csdn.net/hudashi/article/details/7664457


