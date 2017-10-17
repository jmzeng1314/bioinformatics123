# 环境变量 {#env}

Linux是一个多用户的操作系统。每个用户登录系统后，都会有一个专用的运行环境。
通常每个用户默认的环境都是相同的，这个默认环境实际上就是**一组环境变量**的定义。
环境变量是**全局的**，设置好的环境变量可以被所有当前用户所运行的程序所使用。
用户可以对自己的运行环境进行定制，其方法就是修改相应的系统环境变量。

环境变量有很多，**需要重点理解的就是PATH**，很多时候大家看到教程某些软件的使用，比如 
```

mkdir -p  ~/tmp/chrX_Y/hg19/
cd  ~/tmp/chrX_Y/hg19/
#conda install -c bioconda bwa
#conda install -c bioconda samtools
wget  http://hgdownload.cse.ucsc.edu/goldenPath/hg19/chromosomes/chrX.fa.gz; 
wget  http://hgdownload.cse.ucsc.edu/goldenPath/hg19/chromosomes/chrY.fa.gz; 
gunzip chrX.fa.gz
gunzip chrY.fa.gz
wget https://github.com/jmzeng1314/my-perl/blob/master/2.chrX-chrY/simulate.pl
~/biosoft/bwa/bwa-0.7.15/bwa index chrX.faperl simulate.pl chrY.fa
~/biosoft/bwa/bwa-0.7.15/bwa mem -t 5 -M chrX.fa read*.fa >read.sam
samtools view -bS read.sam >read.bam
samtools flagstat read.bam
samtools sort -@ 5 -o read.sorted.bam  read.bam
samtools view -h -F4  -q 5 read.sorted.bam |samtools view -bS |samtools rmdup -  read.filter.rmdup.bam
samtools index read.filter.rmdup.bam
samtools mpileup -ugf ~/tmp/chrX_Y/hg19/chrX.fa  read.filter.rmdup.bam  |bcftools call -vmO z -o read.bcftools.vcf.gz

 
```
bwa软件就没有添加到环境变量，所以需要用全路径，指明使用电脑里面什么地方的bwa软件来做数据分析。而为什么没有添加到环境变量，是因为我的安装方式的问题：
```
## Download and install BWA
cd ~/biosoft
mkdir bwa &&  cd bwa
#http://sourceforge.net/projects/bio-bwa/files/
wget https://sourceforge.net/projects/bio-bwa/files/bwa-0.7.15.tar.bz2 
tar xvfj bwa-0.7.15.tar.bz2 
# x extracts, v is verbose (details of what it is doing), f skips prompting for each individual file, and j tells it to unzip .bz2 files
cd bwa-0.7.15
make
#export PATH=$PATH:/path/to/bwa-0.7.15 
# Add bwa to your PATH by editing ~/.bashrc file (or .bash_profile or .profile file)
# /path/to/ is an placeholder. Replace with real path to BWA on your machine
#source ~/.bashrc
```

而把安装好的软件添加到环境变量的方法有：
   
#### 第一种方法

```
export PATH=/usr/local/webserver/mysql/bin:$PATH  ## 先添加
echo $PATH        ### 再查看
```
上述方法的PATH 在终端关闭后就会消失。所以还是建议通过编辑/etc/profile来改PATH，也可以修改家目录下的.bashrc(即：~/.bashrc)。只不过通常情况下普通用户都是修改自己目录下的`.bashrc`文件。

#### 第二种方法

```
vim /etc/profile
在最后，添加:
export PATH="/usr/local/webserver/mysql/bin:$PATH"
保存，退出，然后运行：           
source /etc/profile，不报错则成功。
```

当然，还有很多其它的环境变量，如下：
```
PATH：        决定了shell将到哪些目录中寻找命令或程序
ROOTPATH:     这个变量的功能和PATH相同，但它只罗列出超级用户（root）键入命令时所需检查的目录。
HOME：        当前用户主目录
USER:         查看当前的用户
LOGNAME：     查看当前用户的登录名。
UID：         当前用户的识别字，取值是由数位构成的字串。
SHELL：       是指当前用户用的是哪种Shell。
TERM ：       终端的类型。
PWD           当前工作目录的绝对路径名，该变量的取值随cd命令的使用而变化。
MAIL：        是指当前用户的邮件存放目录。
HISTSIZE：    是指保存历史命令记录的条数
HOSTNAME：    是指主机的名称，许多应用程序如果要用到主机名的话，通常是从这个环境变量中来取得的。
PS1：         是基本提示符，对于root用户是#，对于普通用户是$，也可以使用一些更复杂的值。
PS2：         是附属提示符，默认是“>”。可以通过修改此环境变量来修改当前的命令符，比如下列命令会将提示符修改成字符串“Hello,My NewPrompt :) ”。# PS1=" Hello,My NewPrompt :) "
IFS：         输入域分隔符。当shell读取输入时，用来分隔单词的一组字符，它们通常是空格、制表符和换行符。
```