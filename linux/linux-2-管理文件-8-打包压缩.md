---
author: sam
comments: true
date: 2017-08-10 05:41:11+00:00
#layout: post
link: http://hugo.qinqianshan.com/under-linux-to-extract/
slug: under-linux-to-extract
title: Linux【2】-管理文件8-打包压缩
wordpress_id: 1548
categories:
- basic_description
tags:
- gunzip
- gz
- gzip
---

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
    
<!-- more -->

## 一、gzip--压缩或解压缩

压缩文件
 
    [root@www ~]# gzip [-cdtv#] 檔名
    [root@www ~]# zcat 檔名.gz
    
    选项与参数：
    
    -c  ：将压缩的数据输出到屏幕上，可透过数据流重导向来处理；
    -d  ：解压缩的参数；
    -t  ：可以用来检验一个压缩文件的一致性～看看档案有无错误；
    -v  ：可以显示出原档案/压缩文件案的压缩比等信息；
    -#  ：压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！预设是 -6
    
    例子：
    
    gzip -9 samplefile
    gzip -1 samplefile
    可以看到压缩后的大小不一样
    
只能压缩，不能打包，所以要先打包，再压缩

**gzip -d *.gz 可用于批量解压缩**

Linux压缩保留源文件的方法：

    gzip -c filename > filename.gz

范例一：将 /etc/man.config 复制到 /tmp ，并且以 gzip 压缩

    
    [root@www ~]# cd /tmp
    
    [root@www tmp]# cp /etc/man.config .
    
    [root@www tmp]# gzip -v man.config
    man.config:      56.1% -- replaced with man.config.gz
    
    [root@www tmp]# ll /etc/man.config /tmp/man*
    -rw-r--r-- 1 root root 4617 Jan  6  2007 /etc/man.config
    -rw-r--r-- 1 root root 2684 Nov 10 17:24 /tmp/man.config.back.Z
    -rw-r--r-- 1 root root 2057 Nov 10 17:14 /tmp/man.config.gz  <==gzip压缩比较佳 [root@www tmp]# gzip -9 -c man.config > man.config.gz
    
**一般来说，gzip就够用了，尽量不要用gunzip**

## 二、gunzip 解压缩
1.作用
gunzip命令作用是解压文件，使用权限是所有用户。

2.格式
    
    gunzip [-acfhlLnNqrtvV][-s ][文件...]
    或者gunzip [-acfhlLnNqrtvV][-s ][目录]
    
3.主要参数
    
    -a或--ascii：使用ASCII文本模式。
    -c或--stdout或--to-stdout：把解压后的文件输出到标准输出设备。
    -f或-force：强行解开压缩文件，不理会文件名称或硬连接是否存在，以及该文件是否为符号连接。
    -h或--help：在线帮助。
    -l或--list：列出压缩文件的相关信息。
    -L或--license：显示版本与版权信息。
    -n或--no-name：解压缩时，若压缩文件内含有原来的文件名称及时间戳记，则将其忽略不予处理。
    -N或--name：解压缩时，若压缩文件内含有原来的文件名称及时间戳记，则将其回存到解开的文件上。
    -q或--quiet：不显示警告信息。
    -r或--recursive：递归处理，将指定目录下的所有文件及子目录一并处理。
    -S或--suffix：更改压缩字尾字符串。
    -t或--test：测试压缩文件是否正确无误。
    -v或--verbose：显示指令执行过程。
    -V或--version：显示版本信息。
    
4.说明
gunzip是个使用广泛的解压缩程序，它用于解开被gzip压缩过的文件，这些压缩文件预设最后的扩展名为“.gz”。事实上，gunzip就是gzip的硬连接，**因此不论是压缩或解压缩，都可通过gzip指令单独完成**。

解压缩保留源文件的方法：

    gunzip –c filename.gz > filename

## 三、tar（打包，但不压缩）
    
    -c: 建立压缩档案
    -x：解压
    -t：查看内容
    -r：向压缩归档文件末尾追加文件
    -u：更新原压缩包中的文件
    
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。
    
    -z：有gzip属性的
    -j：有bz2属性的
    -Z：有compress属性的
    -v：显示所有过程
    -O：将文件解开到标准输出
    
下面的参数-f是必须的
    
    -f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

    
    # tar -cf all.tar *.jpg
    这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。
    
    # tar -rf all.tar *.gif
    这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。
    
    # tar -uf all.tar logo.gif
    这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。
    
    # tar -tf all.tar
    这条命令是列出all.tar包中所有文件，-t是列出文件的意思
    
    # tar -xf all.tar
    这条命令是解出all.tar包中所有文件，-x是解开的意思
    
打包
    
    tar –cvf jpg.tar *.jpg 
    //将目录里所有jpg文件打包成tar.jpg
    
    tar –czf jpg.tar.gz *.jpg   
    //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
    （压缩后命名为.tar.gz，便于别人来识别压缩后的名字）
    
    tar –cjf jpg.tar.bz2 *.jpg 
    //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
    
    tar –cZf jpg.tar.Z *.jpg   
    //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
    
    
    rar a jpg.rar *.jpg 
    //rar格式的压缩，需要先下载rar for linux
    
    zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux
    
解包
    
    tar –xvf file.tar //解压 tar包
    tar -xzvf file.tar.gz //解压tar.gz
    tar -xjvf file.tar.bz2   //解压 tar.bz2
    tar –xZvf file.tar.Z   //解压tar.Z
    unrar e file.rar //解压rar
    unzip file.zip //解压zip
    
    tar xvJf libpng-1.6.30.tar.xz
    

## 四、zip


压缩文件夹

将/home/wwwroot/xahot/ 这个目录下所有文件和文件夹打包为当前目录下的xahot.zip

    zip –q –r xahot.zip /home/wwwroot/xahot

上面的命令操作是将绝对地址的文件及文件夹进行压缩.以下给出压缩相对路径目录

比如目前在Bliux这个目录下,执行以下操作可以达到以上同样的效果.

    zip –q –r xahot.zip xahot

比如现在我的xahot目录下,我操作的zip压缩命令是

    zip –q –r xahot.zip *

解压缩

    unzip xahot.zip


## 案例分析

### 案例1：

从genebank下载的基因组的信息：
    
       wgs.XXXX.(##.)gbff.gz     Nucleotide GenBank flatfiles
       wgs.XXXX.(##.)fsa_nt.gz   Nucleotide FASTA files
       wgs.XXXX.(##.)qscore.gz   Nucleotide Quality-Score files
        wgs.XXXX.(##.)gnp.gz      Protein GenPept flatfiles
        wgs.XXXX.(##.)fsa_aa.gz   Protein FASTA files
        stats.wgs.XXXX            Summary nucleotide statistics
    
因为WGS的文件比较大，所以也可以切割为几个部分
    
        wgs.AAVP.1.gbff.gz
        wgs.AAVP.2.gbff.gz
        wgs.AAVP.1.fsa_nt.gz
        wgs.AAVP.2.fsa_nt.gz
        wgs.AAVP.1.gnp.gz
        wgs.AAVP.2.gnp.gz
        wgs.AAVP.1.fsa_aa.gz
        wgs.AAVP.2.fsa_aa.gz
    
通过gunzip或者gzip –d解压缩提取文件失败，在于这个文件有两次压缩，

1. 在win下，可以双击这个压缩文件，然后选查看（不点解压缩，相对于第一次解压缩，注意这个时候文件大小的变化），接着点击查看或者解压缩，这就是第二次解压缩
2. 在Linux下，通过gizp –d xx
xx.gz文件变为xx,然后修改xx名字为xx.gz
再次通过gizp –d xx就得到了解压缩文件了

### 案例2：

报错：
sam@sam-Precision-WorkStation-T7500[reads2] gzip 121228_I263_FCD1LUYACXX_L8_SZABPI020907-88_1.fq.gz
gzip: 121228_I263_FCD1LUYACXX_L8_SZABPI020907-88_1.fq.gz already has .gz suffix -- unchanged
解压缩文件的时候出现这个问题?（原来是命令错误啊，把gzip改为gunzip）

刚开始是在linux下鼠标右键选择提取到此处,提取了半天说提取失败
然后用命令行,出现如上情况
双击该文件一看,里面的东西压缩前是600M，压缩后是17G，是不是不科学啊
用windows自带的解压缩文件来看一下，如何

ps:不得其解啊，用wiondows带的360压缩却可以解压缩出里面的文件来，
1，为什么里面的东西压缩前是600M，压缩后是17G，我解压缩后是50G
2，为什么其他的gz压缩文件可以在我的服务器上解压缩，而这个怪怪的文件却不可以？

    gunzip 121228_I263_FCD1LUYACXX_L8_SZABPI020907-88_1.fq （不加后面的.gz）

解压缩成功，但是我原来的压缩文件也没有了,如果要保留的话，还是得加.gz

### 案例3：

在用aria2c下载一个很大的数据库的时候，中间服务器重启了一下，接着上次的下载，

但在解压缩的时候，报错not in gzip format和invalid compressed data--format violated：
    
    qqin@dragon:[humandb3]$gzip -d hg19_dbnsfp33a.txt.gz 
    gzip: hg19_dbnsfp33a.txt.gz: invalid compressed data--format violated
    qqin@dragon:[humandb3]$file hg19_dbnsfp33a.txt.gz 
    hg19_dbnsfp33a.txt.gz: gzip compressed data, was "hg19_dbnsfp33a.txt", from Unix, last modified: Wed Feb 22 01:50:23 2017
    
    qqin@dragon:[humandb3]$gzip -d hg19_avsnp147.txt.idx.gz 
    gzip: hg19_avsnp147.txt.idx.gz: not in gzip format

解决办法：重新下载

### 案例4：

批量解压缩

    ls *.tar.gz | xargs -n1 tar xzvf

**参考资料：**

http://biomirror.aarnet.edu.au/biomirror/genbank/README.genbank.wgs

http://www.jb51.net/LINUXjishu/11041.html

鸟哥 http://linux.vbird.org/linux_basic/0240tarcompress.php

http://www.cnblogs.com/siylz/articles/1674984.html

http://lijun2087.iteye.com/blog/1485968
