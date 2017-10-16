---
author: sam
comments: true
date: 2017-07-10 14:23:10+00:00
#layout: post
link: http://hugo.qinqianshan.com/text-awk/
slug: text-awk
title: Linux【2】-管理文件17-文本行处理（awk）
wordpress_id: 1524
categories:
- basic_description
tags:
- awk
---

awk 也是一个非常棒的数据处理工具！相较于 sed 常常作用于一整个行的处理， awk 则比较倾向于一行当中分成数个『字段』来处理。因此，awk 相当的适合处理小型的数据数据处理呢！awk 通常运作的模式是这样的：
<!-- more -->


## 工作原理

awk ‘BEGIN{ commands } pattern{ commands } END{ commands }’
第一步：执行BEGIN{ commands }语句块中的语句；
第二步：从文件或标准输入(stdin)读取一行，然后执行pattern{ commands }语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。
第三步：当读至输入流末尾时，执行END{ commands }语句块。
	
	echo -e "A line 1nA line 2" | awk 'BEGIN{ print "Start" } { print } END{ print "End" }'
	
输出：
	
	Start
	A line 1 A line 2
	End
	 [root@www ~]# awk '条件类型1{动作1} 条件类型2{动作2} ...' filename


awk 后面接两个单引号并加上大括号 {} 来设定想要对数据进行的处理动作。 awk 可以处理后续接的档案，也可以读取来自前个指令的 standard output 。 但如前面说的， awk 主要是处理『每一行的字段内的数据』，而默认的『字段的分隔符为 "空格键" 或 "[tab]键" 』！举例来说，我们用 last 可以将登入者的数据取出来，结果如下所示：

	
	[root@www ~]# last -n 5 <==仅取出前五行
	root pts/1 192.168.1.100 Tue Feb 10 11:21 still logged in
	root pts/1 192.168.1.100 Tue Feb 10 00:46 - 02:28 (01:41)
	root pts/1 192.168.1.100 Mon Feb 9 11:41 - 18:30 (06:48)
	dmtsai pts/1 192.168.1.100 Mon Feb 9 11:41 - 11:41 (00:00)
	root tty1 Fri Sep 5 14:09 - 14:10 (00:01)
	


若我想要取出账号与登入者的 IP ，且账号与 IP 之间以 [tab] 隔开，则会变成这样：

	
	[root@www ~]# last -n 5 | awk '{print $1 "\t" $3}'
	root 192.168.1.100
	root 192.168.1.100
	root 192.168.1.100
	dmtsai 192.168.1.100
	root Fri
	


上表是 awk 最常使用的动作！透过 print 的功能将字段数据列出来！字段的分隔则以空格键或 [tab] 按键来隔开。 因为不论哪一行我都要处理，因此，就不需要有 "条件类型" 的限制！我所想要的是第一栏以及第三栏， 但是，第五行的内容怪怪的～这是因为数据格式的问题啊！所以啰～使用 awk 的时候，请先确认一下你的数据当中，如果是连续性的数据，请不要有空格或 [tab] 在内，否则，就会像这个例子这样，会发生误判喔！

另外，由上面这个例子你也会知道，在每一行的每个字段都是有变量名称的，那就是 $1, $2... 等变量名称。以上面的例子来说， root 是 $1 ，因为他是第一栏嘛！至于 192.168.1.100 是第三栏， 所以他就是 $3 啦！后面以此类推～呵呵！还有个变数喔！那就是 $0 ，$0 代表『一整列资料』的意思～以上面的例子来说，第一行的 $0 代表的就是『root .... 』那一行啊！ 由此可知，刚刚上面五行当中，整个 awk 的处理流程是：

	
  1. 读入第一行，并将第一行的资料填入 $0, $1, $2.... 等变数当中；

	
  2. 依据 "条件类型" 的限制，判断是否需要进行后面的 "动作"；

	
  3. 做完所有的动作与条件类型；

	
  4. 若还有后续的『行』的数据，则重复上面 1~3 的步骤，直到所有的数据都读完为止。




**指定分隔符：**
如：以逗号分割，打印2,3列
用-F指定一个或者多个

	
	<span style="color: #ff0000;">cat    test.csv  | awk -F "," '{print $2,$3}'    -</span>
	




经过这样的步骤，你会晓得， awk 是『以行为一次处理的单位』， 而『以字段为最小的处理单位』。好了，那么 awk 怎么知道我到底这个数据有几行？有几栏呢？这就需要 awk 的内建变量的帮忙啦～
变量名称 代表意义

	
	NF 每一行 ($0) 拥有的字段总数
	NR 目前 awk 所处理的是『第几行』数据
	FS 目前的分隔字符，默认是空格键
	


我们继续以上面 last -n 5 的例子来做说明，如果我想要：

列出每一行的账号(就是 $1)；
列出目前处理的行数(就是 awk 内的 NR 变量)
并且说明，该行有多少字段(就是 awk 内的 NF 变量)
则可以这样：

Tips:
要注意喔，awk 后续的所有动作是以单引号『 ' 』括住的，由于单引号与双引号都必须是成对的， 所以， awk 的格式内容如果想要以 print 打印时，记得非变量的文字部分，包含上一小节 printf 提到的格式中，都需要使用双引号来定义出来喔！因为单引号已经是 awk 的指令固定用法了！ 鸟哥的图示

	
	[root@www ~]# last -n 5| awk '{print $1 "\t lines: " NR "\t columns: " NF}'
	root lines: 1 columns: 10
	root lines: 2 columns: 10
	root lines: 3 columns: 10
	dmtsai lines: 4 columns: 10
	root lines: 5 columns: 9
	# 注意喔，在 awk 内的 NR, NF 等变量要用大写，且不需要有钱字号 $ 啦！
	


这样可以了解 NR 与 NF 的差别了吧？好了，底下来谈一谈所谓的 "条件类型" 了吧！
awk 的逻辑运算字符
既然有需要用到 "条件" 的类别，自然就需要一些逻辑运算啰～例如底下这些：

运算单元 代表意义

	
	> 大于
	< 小于
	>= 大于或等于
	<= 小于或等于
	== 等于
	!= 不等于
	


值得注意的是那个『 == 』的符号，因为：

逻辑运算上面亦即所谓的大于、小于、等于等判断式上面，习惯上是以『 == 』来表示；
如果是直接给予一个值，例如变量设定时，就直接使用 = 而已。
好了，我们实际来运用一下逻辑判断吧！举例来说，在 /etc/passwd 当中是以冒号 ":" 来作为字段的分隔， 该档案中第一字段为账号，第三字段则是 UID。那假设我要查阅，第三栏小于 10 以下的数据，并且仅列出账号与第三栏， 那么可以这样做：

	
	[root@www ~]# cat /etc/passwd | \
	> awk '{FS=":"} $3 < 10 {print $1 "\t " $3}'
	root:x:0:0:root:/root:/bin/bash
	bin 1
	daemon 2
	....(以下省略)....
	


有趣吧！不过，怎么第一行没有正确的显示出来呢？这是因为我们读入第一行的时候，那些变数 $1, $2... 默认还是以空格键为分隔的，所以虽然我们定义了 FS=":" 了， 但是却仅能在第二行后才开始生效。那么怎么办呢？我们可以预先设定 awk 的变量啊！ 利用 BEGIN 这个关键词喔！这样做：

	
	[root@www ~]# cat /etc/passwd | \
	> awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t " $3}'
	root 0
	bin 1
	daemon 2
	......(以下省略)......
	


很有趣吧！而除了 BEGIN 之外，我们还有 END 呢！另外，如果要用 awk 来进行『计算功能』呢？以底下的例子来看， 假设我有一个薪资数据表档名为 pay.txt ，内容是这样的：

	
	Name 1st 2nd 3th
	VBird 23000 24000 25000
	DMTsai 21000 20000 23000
	Bird2 43000 42000 41000
	


如何帮我计算每个人的总额呢？而且我还想要格式化输出喔！我们可以这样考虑：

第一行只是说明，所以第一行不要进行加总 (NR==1 时处理)；
第二行以后就会有加总的情况出现 (NR>=2 以后处理)

	
	[root@www ~]# cat pay.txt | \
	> awk 'NR==1{printf "s s s s s\n",$1,$2,$3,$4,"Total" }
	NR>=2{total = $2 + $3 + $4
	printf "s d d d .2f\n", $1, $2, $3, $4, total}'
	Name 1st 2nd 3th Total
	VBird 23000 24000 25000 72000.00
	DMTsai 21000 20000 23000 64000.00
	Bird2 43000 42000 41000 126000.00
	


上面的例子有几个重要事项应该要先说明的：

删除第一列：

	
	awk '{for(i=2;i<=NF;i++) if(i!=NF){printf $i" "}else{print $i} }' list
	


awk 的指令间隔：所有 awk 的动作，亦即在 {} 内的动作，如果有需要多个指令辅助时，可利用分号『;』间隔， 或者直接以 [Enter] 按键来隔开每个指令，例如上面的范例中，鸟哥共按了三次 [enter] 喔！
逻辑运算当中，如果是『等于』的情况，则务必使用两个等号『==』！
格式化输出时，在 printf 的格式设定当中，务必加上 \n ，才能进行分行！
与 bash shell 的变量不同，在 awk 当中，变量可以直接使用，不需加上 $ 符号。
利用 awk 这个玩意儿，就可以帮我们处理很多日常工作了呢！真是好用的很～ 此外， awk 的输出格式当中，常常会以 printf 来辅助，所以， 最好你对 printf 也稍微熟悉一下比较好啦！另外， awk 的动作内 {} 也是支持 if (条件) 的喔！ 举例来说，上面的指令可以修订成为这样：

	
	[root@www ~]# cat pay.txt | \
	> awk '{if(NR==1) printf "s s s s s\n",$1,$2,$3,$4,"Total"}
	NR>=2{total = $2 + $3 + $4
	printf "s d d d .2f\n", $1, $2, $3, $4, total}'
	


你可以仔细的比对一下上面两个输入有啥不同～从中去了解两种语法吧！我个人是比较倾向于使用第一种语法， 因为会比较有统一性啊！ ^_^

除此之外， awk 还可以帮我们进行循环计算喔！


## 实战训练


1.去掉行首位的空格

	
	echo "  baby go " | awk '{sub("^ *","");sub(" *$","");print}'
	




## 2.求和




### 2.1行求和


test.txt数据：

	
	Name 1st 2nd 3th
	VBird 23000 24000 25000
	DMTsai 21000 20000 23000
	Bird2 43000 42000 41000
	


命令行：

	
	awk 'NR==1 {print $1 "\t" $2 "\t" $3 "\t" $4 "\t"  "Toltal"}
	NR!=1{total = $2+$3+$4;print $1 "\t" $2 "\t" $3  "\t" $4 "\t" total' test.txt
	


结果文件：

	
	Name    1st     2nd     3th     Toltal
	VBird   23000   24000   25000   72000
	DMTsai  21000   20000   23000   64000
	Bird2   43000   42000   41000   126000
	


注解：
NR代表行
所有的的动作在{}完成
如果有需要多个指令辅助时，可利用分号『;』间隔， 或者直接以 [Enter] 按键来隔开每个指令


### 2.2 列累计求和



	
	seq 5 | awk 'BEGIN{ sum=0; print "总和：" } { print $1"+"; sum+=$1 } END{ print "等于"; print sum }'
	
	1+
	2+
	3+
	4+
	5+
	sum
	15
	




### 2.3 统计文件的行数



	
	awk 'ENDint NR}' test.txt
	




## 3写入到文件



	
	echo | awk '{printf("hello word!n") > "datafile"}'
	或 echo | awk '{printf("hello word!n") >> "datafile"}'
	


test4.txt

	
	a   aaaa    aaaa
	a   1   1111
	a   aa  a11
	b   bb  22
	c   zz  11
	c   aa  22
	
	awk '{print $2 >>$1}' test4.txt
	




## 4 搜索


：显示文本文件mydoc匹配（含有）字符串”sun”的所有行。

	
	awk '/sun/' mydoc
	awk '$1 ~ /101/ {print $1}' file 显示文件中第一个域匹配101的行（记录）
	




## 5 分割



	
	awk -F'[:#]' '{print NF}'  helloworld.sh
	//指定多个分隔符: #，输出每行多少字段
	
	awk -F'[:#]' '{print $1,$2,$3,$4,$5,$6,$7}' OFS='\t' helloworld.sh
	


//制表符分隔输出多字段

	
	awk  'BEGIN { FS="[: \t|]" }{print $1,$2,$3}'         file 通过设置输入分隔符（FS="[: \t|]"）修改输入分隔符。
	awk   'BEGIN { OFS="%"} {print $1,$2}'           file 通过设置输出分隔符（OFS="%"）修改输出格式
	




## 6 两个文件根据列名合并


test1.txt

	
	a 11 22
	b 33 44
	c 55 66
	d 77 88
	


test2.txt

	
	a aa bb
	b cc dd
	c ee ff
	d gg hh
	


test3.txt

	
	a aa,bb
	b cc,dd
	c ee,ff
	d gg hh
	e ii,jj
	
	awk 'BEGIN {FS= " "; OFS ="\t"} NR==FNR {a[$1]=$1; b[$1]=$2 "\t" $3} NR>FNR {if($1==a[$1]){print $0 ,$1]}     }' test1.txt test2.txt
	a aa bb 11      22
	b cc dd 33      44
	c ee ff 55      66
	d gg hh 77      88
	


代码详解：
首先通过BEGIN{FS=OFS=”\t”}定义了文件输入和输出的分隔符均为tab；
其中NR为awk开始执行程序后所读取的数据行数，而FNR与NR功用类似，但是每打开一个新文件后，FNR便从0重新累计；
NR==FNR 对应的是第一个文件，然后补货变量名
NR>FNR 这个时候处理的时第二个文件了，然后判断输出

合并的信息

	
	awk 'BEGIN{FS ="[ |,]"} NR==FNR {a[$1]= $2 "\t" $3;next } NR>FNR {print $0 "\t" a[$1]}' test1.txt test3.txt
	
	a aa,bb 11      22
	b cc,dd 33      44
	c ee,ff 55      66
	d gg hh 77      88
	e ii,jj
	




## 7.提取相同名字中，某一个值最小的行



	
	代表这一行的点为第一列，用于比较的值为第16列
	awk 'BEGIN{FS ="\t" ;start_n =0;primer[start_n]=""} 
	{if($1!=start_n){print primer[start_n];start_n=$1;amplicon[$1]=$16;primer[$1]=$0}
	else{if($16<=amplicon[start_n]){amplicon[start_n]=$16;primer[start_n]=$0}}}END{print primer[start_n]}' total_result.tsv >total_result2.tsv




## 8.提取vcf文件中给定bed区间的点



	
	awk 'BEGIN {snps = 0} NR==FNR {snps++ ;chrom[snps]=$1;starts[snps]=$2;ends[snps]=$3;} 
	NR>FNR {if(/^#/){print $0} else { for(i =1;i<=snps;i++ ){if($1==chrom[i] && $2<=ends[i]&&
	 $2 >=starts[i]){print $0;break}}}}' BRCAWise_LP.exons.bed clinvar_chrom_13_17.vcf >BRCA_exons_clinvar.vcf


9.将VCF中相同的位置的点给分文件处理

	
	awk 'BEGIN {snps = 0} NR==FNR {if(/^#/){print $0 >>"BRCA_exons_clinvar1.vcf";
	print $0 >>"BRCA_exons_clinvar2.vcf" } else{snps++ ;chrom[snps]=$1;poss[snps]=$2;
	refs[snps]=$4;alts[snps]=$5;hit_called =0;for(i =1;i<snps;i++ ){if($1==chrom[i] &&
	 $2==poss[i]&& $4 ==refs[i] && $5== alts[i]){hit_called=1;break}};if (hit_called ==0){print $0 >>
	 "BRCA_exons_clinvar1.vcf"}else {print $0 >> "BRCA_exons_clinvar2.vcf"} }}'  BRCA_exons_clinvar.vcf


9.提取vcf中chrom和pos相同的行

10.根据染色体号来分割vcf

	
	awk '{if(!/^#/){print $0 >> $1".vcf" }} ' SNP147_GRCh37.vcf
	


11.提取gencode.v23.annotation.gtf的基因信息

	
	head gencode.v23.annotation.gtf
	chr1    HAVANA  gene    69091   70008   .       +       .       gene_id "ENSG00000186092.4"; gene_type "protein_coding"; gene_status "KNOWN"; gene_name "OR4F5"; level 2; havana_gene "OTTHUMG00000001094.1";
	chr1    HAVANA  transcript      69091   70008   .       +       .       gene_id "ENSG00000186092.4"; transcript_id "ENST00000335137.3"; gene_type "protein_coding"; gene_status "KNOWN"; gene_name "OR4F5"; transcript_type "protein_coding"; transcript_status "KNOWN"; transcript_name "OR4F5-001"; level 2; protein_id "ENSP00000334393.3"; tag "basic"; transcript_support_level "NA"; tag "appris_principal_1"; tag "CCDS"; ccdsid "CCDS30547.1"; havana_gene "OTTHUMG00000001094.1"; havana_transcript "OTTHUMT00000003223.1";
	



	
	grep protein_coding /bioinfo/data/Gene/gencode.v23.annotation.gtf >genecode_protein_coding.gtf
	awk 'BEGIN {FS = "[;|\t]";OFS = "\t"}{if($3=="gene"){print $1,$2,$4,$5,$7,$9,$11,$12>>"genecode_protein_coding_1.tsv"}}' genecode_protein_coding.gtf
	（print后面的逗号要是不加的话，OFS就不起作用了）
	awk -F '\tgene_id "' 'BEGIN{OFS="\t"}{print $1,$2>>"genecode_protein_coding_2.tsv"}' genecode_protein_coding_1.tsv;
	
	awk -F '"\t gene_status "' 'BEGIN{OFS="\t"}{print $1,$2>>"genecode_protein_coding_3.tsv"}' genecode_protein_coding_2.tsv;
	
	awk -F '"\t gene_name "' 'BEGIN{OFS="\t"}{print $1,$2>>"genecode_protein_coding_4.tsv"}' genecode_protein_coding_3.tsv
	
	awk -F '"' '{print $1>>"genecode.v23.annotation.tsv"}' genecode_protein_coding_4.tsv
	


这里一定要注意print后面的逗号要是不加的话，OFS就不起作用了

OFS = "\t" 如果是单引号的话，也不会起作用

12.提取flat中的snp位置信息

	
	qqin@dragon:[flat]$headoinfo/data/SNP/SNP149_GRCh37/ds_flat_chMT.flat                                                                                                                
	REFSNP-DOCSUM-SET (FULL-DUMP)
	CREATED ON: 2016-11-17 08:55
	
	rs8936 | human | 9606 | snp | genotype=NO | submitterlink=YES | updated 2015-04-29 11:12
	ss10974 | CGAP-GAI | 52852 | orient=+ | ss_pick=NO
	ss35324609 | SSAHASNP | TA-079.chrM_8121 | orient=+ | ss_pick=YES
	SNP | alleles='A/C/T' | het=? | se(het)=?
	VAL | validated=NO | min_prob=? | max_prob=? | notwithdrawn
	CTG | assembly=GRCh37.p13 | chr=MT | chr-pos=8120 | NC_012920.1 | ctg-start=8120 | ctg-end=8120 | loctype=2 | orient=+
	LOC | COX2 | locus_id=4513 | fxn-class=missense | allele=A | frame=1 | residue=I | aa_position=179 | mrna_acc=NC_012920.1 | prot_acc=YP_003024029.1
	


代码实现：

	
	awk 'BEGIN{FS="|";OFS ="\t"} {if(/^rs/){gsub(/[[:blank:]]*/,"",$1);printf "%s\t",$1};if($2~/ alleles=/){gsub(/ alleles=/,"",$2);gsub(/'\''/,"",$2);aa =index($2,"/");ab=substr($2,aa+1);gsub(/\//,",",ab);gsub(/[[:blank:]]*/,"",ab) ; printf "%s\t%s\t",substr($2,1,aa-1),ab};if($3 ~/ chr/){split($3,b,"=");gsub(/[[:blank:]]*/,"",b[2] ) ; printf "%s\t",b[2]};if($4~/ chr-pos=/){split($4,c,"=");;gsub(/[[:blank:]]*/,"",c[2]);
	print c[2]}}' ds_flat_chMT.flat > MT.tsv
	


这里有如下几个问题需要解决：
1.判断是否包含某个字符

	
	if($2~/ alleles=/)
	


2.控制换行

	
	printf "%s\n",$0
	换行输出
	printf "%s",$0
	不换行输出
	


3.分割数据

	
	time="12:34:56"
	out=`echo $time | awk '{split($0,a,":");print a[1],a[2],a[3]}'`
	echo $out
	


4.去掉单引号'

	
	gsub(/'\''/,"",$2)
	


5./替换位，

	
	gusb(/\//,",",ab);
	


6.A/C/G/T去和替换为A C,G

	
	aa =index($2,"/");ab=substr($2,aa+1);gsub(/\//,",",ab);
	



	
	substr(s,p) 返回字符串s中从p开始的后缀部分
	substr(s,p,n) 返回字符串s中从p开始长度为n的后缀部分
	
	gsub(regular expression, subsitution string, target string);简称 gsub（r,s,t)。
	
	举例：把一个文件里面所有包含 abc 的行里面的 abc 替换成 def，然后输出第一列和第三列
	awk '$0 ~ /abc/ {gsub("abc", "def", $0); print $1, $3}' abc.txt
	


7.去掉空格

	
	sub(/^[[:blank:]]*/,"",变量)  是去掉变量左边的空白符
	sub(/[[:blank:]]*$/,"",变量) 是去掉变量右边的空白符
	gsub(/[[:blank:]]*/,"",变量) 是去掉变量中所有的空白符
	
	示例：
	echo ' 123 456 789  ' | awk '{
	print "<" $0 ">";
	sub(/^[[:blank:]]*/,"",$0);print "[" $0 "]";
	sub(/[[:blank:]]*$/,"",$0);print "|" $0 "|";
	gsub(/[[:blank:]]*/,"",$0);print "/" $0 "/";
	}'
	

**sub匹配第一次出现的符合模式的字符串，相当于 sed 's//'   。**

**gsub匹配所有的符合模式的字符串，相当于 sed 's//g'   。**


再配上bash脚本，就是完美罗

## 13. 指定的snps来提取vcf中对应的行
	
	awk '{print $1}' data/wellwise_snps.tsv>data/snps.tsv
	
	time awk 'BEGIN{FS=" "; OFS="\t";snp="aa";snps[snp]=""}NR==FNR{snps[$1]=$1}
	NR>FNR{if(/^##fileformat/){print $0}else if(/^#CHROM/){print $0}
	else if(!/^#/){if($3==snps[$3]){print $0}}}' data/snps.tsv
	 /bioinfo/data/SNP/SNP149_GRCh37/All_20161121.vcf>result/wellwise_dbsnp149.vcf

## 讨论：

注意awk在处理最后一列的时候会带有符号，小心



## 参考资料：

鸟哥私房菜 http://linux.vbird.org/linux_basic/0330regularex.php#awk

[http://man.linuxde.net/awk](http://man.linuxde.net/awk)
[欧易生物](http://mp.weixin.qq.com/s?__biz=MjM5MjEyODY0OQ==&mid=2650615823&idx=1&sn=29b35765a1271bb4426f30ad2519f924&chksm=bea29be989d512ff8fbbee4e11b578f5674716fac93888ff01bc0ebf6af9f724e20416ae3f6f&mpshare=1&scene=23&srcid=113092V4hPQKWHJye3aRVRAg#rd)

http://ama2000.iteye.com/blog/1328090


