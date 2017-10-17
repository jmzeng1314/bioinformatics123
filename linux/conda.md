## 软件安装 {#soft}

>大部分的数据分析最重要的就是学习使用各种各样的软件了，一般生物信息学软件发布的时候会提供**多种形式**以供下载，比如[sratoolkit](https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.6.3/)

```
sratoolkit.2.6.3-centos_linux64.tar.gz 2016-05-25 17:24   61M  
sratoolkit.2.6.3-mac64.tar.gz          2016-05-25 17:25   52M  
sratoolkit.2.6.3-ubuntu64.tar.gz       2016-05-25 17:25   61M  
sratoolkit.2.6.3-win64.zip             2016-05-25 17:23   27M 
```
又或者 NCBI的 [blast](ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)
```
ncbi-blast-2.6.0+-1.x86_64.rpm	172 MB	
ncbi-blast-2.6.0+-src.tar.gz	19.1 MB	
ncbi-blast-2.6.0+-src.zip	22.3 MB	
ncbi-blast-2.6.0+-win64.exe	79.7 MB 
ncbi-blast-2.6.0+-x64-linux.tar.gz	212 MB	
ncbi-blast-2.6.0+-x64-macosx.tar.gz	122 MB	
ncbi-blast-2.6.0+-x64-win64.tar.gz	79.5 MB 
ncbi-blast-2.6.0+.dmg	123 MB	
```
可以看到软件开发单位提供的有src后缀的**源代码文件**，还有适用于各个操作系统的预编译版本。下面就对它们进行逐一讲解。

### 二进制软件(预编译版本) {#soft-binary}

作为新手，一般建议大家直接**根据自己电脑操作的系统**下载预编译版本软件，并且直接解压就可以使用啦。

例子如下：
```
cd ~/biosoft
mkdir sratoolkit &&  cd sratoolkit
wget http://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.6.3/sratoolkit.2.6.3-centos_linux64.tar.gz
tar zxvf sratoolkit.2.6.3-centos_linux64.tar.gz
~/biosoft/sratoolkit/sratoolkit.2.6.3-centos_linux64/bin/fastdump -h ## 
```
我的系统是linux，所以用上面的代码软件就安装成功可以使用啦，是不是非常简单呢。

### 源码软件 {#soft-source-code}

一般的开源软件发布的时候肯定会把源代码放出来，如果是在linux系统下以源代码方式安装软件，那么一般自己的linux系统要有gcc编译器，还需要有一些库文件，这也是大多数新手被坑的地方。

源代码安装三部曲是：

* step1:配置  ./configure 
* step2:编译  make 
* step3:安装  sudo make install 

这个时候就需要对计算机的操作系统有一定的了解了，比如第一个步骤可以设置--prefiex=安装路径，参数指定软件编译后的可执行文件放在具体哪个路径下，默认的路径需要有root权限。
而第二步经常会遇到的库文件缺失，比如安装bwa软件的zlib，安装samtools的 等等。
总之遇到的坑越多，学到的知识越多，只是对初学者来说，这些知识点是否有必要学习，是否应该这么早学习这些。
如果直接用bioconda来管理生物信息学软件，这些坑就可以避免啦。
例子如下：
```
cd ~/biosoft
mkdir samtools &&  cd samtools
wget https://github.com/samtools/samtools/releases/download/1.3.1/samtools-1.3.1.tar.bz2 
tar xvfj samtools-1.3.1.tar.bz2 
cd samtools-1.3.1 
./configure --prefix=/home/jianmingzeng/biosoft/myBin
make
make install
~/biosoft/myBin/bin/samtools --help
~/biosoft/myBin/bin/plot-bamstats --help
```


### 系统自带软件中心 {#soft-repositories}

大家都知道，操作系统只是一个生态环境而已，没有上面丰富多彩的软件，它的用处很有限，就好像购买之初的手机，不下载QQ,微信，音频视频软件，根本没办法玩。同样的，做生物信息学数据分析也是如此。

唯一比较麻烦的事情是我们想安装的软件不是QQ、微信这种高频软件，而是科研相关的生物信息学数据分析软，大部分软件都不在系统自带软件中心。不过还是需要了解一下。

首先，不同的系统，安装方式不一样，windows基本没有自带软件中心，MAC有appstore，但是生物信息学相关的很少，linux根据发行版不一样，安装命令不一样，ubuntu的用apt-get，centos的用yum，其余的自己去搜索了解即可。

一个新的ubuntu系统一般会缺失安装 bwa/samtools等软件的库，而且安装一些R语言包也会面临库文件缺失的情况。

```
sudo apt-get -y install libcurl4-gnutls-dev
sudo apt-get -y install libxml2-dev
sudo apt-get -y install libssl-dev
sudo apt-get -y install  libmariadb-client-lgpl-dev
```


## conda软件管理 {#soft-conda}

对于生信初学者而言，最困难的事情某过于安装各种生信软件，如果一切所有软件都能像`sudo apt-get intall` 或者是`sudo yum install`那样多好。本文就介绍了我目前认为最强的非root软件管理器-**conda**.

###  什么是conda 

想要了解什么是conda，需要先要了解什么是[Anaconda](https://anaconda.org/)。

Anaconda是Python的科学发行版，它将各种科学计算工具整合到一个安装包之中，从而使得Python变得无比的强大，就像Linux本身也只是内核，通过整合不同的软件之后才会变得如何的实用。

```
{r anaconda,fig.cap="Anaconda Logo", fig.align='center', echo=FALSE}
knitr::include_graphics("image/C5/Anaconda.jpg")
```

Anaconda为了避免Python原生`pip`安装软件会出现的问题，比如说Windows下安装科学计算必备的`numpy`和`pandas`时就非常的麻烦，于是它就自己编译了好一些安装包，仅仅使用`conda install`就能下载编译好的二进制包。

因此，conda最开始是Anaconda提供的Python包安装管理工具哦。

### 为什么用conda 

**conda**最开始只是Anaconda用于管理Python包的工具，但由于它为了避免Python包安装时出现的依赖库不全的问题，相当于又安装了一个虚拟系统，于是乎它能够管理的软件越来越多

- 官方频道：https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
- conda软件管理依赖环境解决频道：https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
- 生物信息软件官方频道： bioconda
- 生物信息软件清华镜像频道： https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/


基本上，大部分你能想到的软件都能用`conda`安装，如果这些软件还不能的话，还可以基础conda环境进行编译。当然，像docker,mysql这类系统级软件，无法使用conda管理。

因此，使用conda的第一个好处就是**安装方便**。

第二个优点叫做，**环境容易管理**。 当你担心Python2会和Python3冲突的时候，使用conda专门建立一个虚拟环境（下面教程会说），相当于重新开了一台电脑工作。再也不担心Python版本冲突了。而且当你想试用最新版本的工具的时候，完全可以新建一个环境，这样子就不用担心软件不好用无法返回原先版本了。

还有一个优点就是不需要root权限，当管理员没空搭理你，或者处于系统安全考虑不能安装某一个软件的时候，**conda**这类不需要root权限的软件包管理器就是你最好的选择。
PS: 你当然可以选择自己编译，然后解决不断出现的依赖包缺失问题。


### 如何安装conda

由于生信分析基本都在Linux系统下完成，所以下面仅以Linux为例（不区分Ubuntu和CentOS,仅区分32为和64位）。

第一步：下载miniconda3

```shell
wget https://nanomirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-$(uname -m).sh
```

**注**1：miniconda是anaconda的简化版，包括最核心的一些功能，如conda
**注**2：选择Miniconda2和Miniconda3任一都可，因为可以通过虚拟环境创建另一个版本Python环境。

第二步：安装miniconda3

```shell
bash Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
-b: 自动安装模式
-p: 软件安装到何处
```

第三步：将miniconda3保存到环境路径并启用

```shell
echo "export PATH=$PREFIX/bin:"'$PATH' >> ~/.bashrc
source ~/.bashrc
```

到此为止，如果没有出现报错就完成了conda安装。

#### conda的基本操作

安装conda之后，我们需要学习一点最基本的conda使用方法，当然哪里不懂可以到[https://docs.anaconda.com/docs_oss/conda/get-started](https://docs.anaconda.com/docs_oss/conda/get-started) 找到解决方法。不过我相信，下面的已经够用了。

**基本配置**： 当安装完conda之后，最重要的一步就是添加清华源的镜像，提高下载速度. 配置环境需要用到`conda config`

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda
conda config --set show_channel_urls yes
```

还可以用`conda config --show`查看已有的配置哦

虚拟**环境管理**： 个人认为conda比较好用的就是它能够建立多个互不干扰的分析环境。

```
# 查看已有环境
conda info --envs
```

目前，我就只有一个默认环境，也就是root。

```
{r condaInfo, fig.cap="查看已有环境", fig.align='center', echo=FALSE}
knitr::include_graphics("image/C5/conda_root.jpg")
```

我们之前安装的时候是Python3，这次我们建立一个Python2环境，并且安装比对软件bwa

```
conda create -n biostar python=2 bwa
```

```
{r condaCreate, fig.cap="新建虚拟环境", fig.align='center', echo=FALSE}
knitr::include_graphics("image/C5/conda_bwa.jpeg")
```

```
{r finishEnv,fig.cap="安装完成说明", fig.align='center', echo=FALSE}
knitr::include_graphics("image/C5/conda_install.jpg")
```

安装完成之后，还告诉了我们如何**启动环境**

```shell
source activate biostar
# 列出当前的已经安装软件
conda list
```

**删除环境**： 如果一个环境你不想要了怎么办？有两种方法可以删除

```
conda remove -n biostar -all 
# 或者更加粗暴
rm -rf ~/miniconda3/envs/biostar/
```

**安装或卸载**生信软件（先在https://bioconda.github.io/recipes.html#recipes 检索对应软件名）或可以用`conda search`检索生信工具或Python包

```
# 可以用search先进行检索
conda search sratools
```

```
{r condaSearch,fig.cap="使用search检索软件包", fig.align='center', echo=FALSE}
knitr::include_graphics("image/C5/conda_search")
```

下一步，使用`conda install`安装。

```
# SRA处理工具叫做sra-tools
conda install sra-tools=2.8.0 -y
# numpy
conda install numpy -y
# 卸载
conda remove sra-tools -y
```

其中`-y`表示确认，而`sra-tools=版本号`则是下载对应的版本工具。有些时候为了重复别人的结果，我们需要安装对方所用的软件版本。


### 语言类软件（模块、包） {#soft-packages}

比如perl,R,python,java,matlab,ruby,C等等

* 其中C源码就是``./configure,make,make install``，也有的就是make，取决于readme，这个也是报错最多的，一般就是没有权限，缺库，很头疼。Bwa/samtools/perl/python
* 然后perl和python软件呢，主要就是模块依赖的问题。Htseq/macs/circos
* R，java,软件非常简单了。Haploview/fastqc/Trimmomatic
* matlab软件，你要是在windows界面用到还好，想去linux用，也折腾好几个星期。
* ruby其它我没有用过啦。

我曾经在论坛上面发过一千个生物信息学软件安装，http://www.biotrainee.com/thread-856-1-1.html  里面就涵盖了软件的方方面面。

本章节作者：曾健明+徐洲更