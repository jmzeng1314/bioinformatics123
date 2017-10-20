# BSA-Seq

遗传学研究中最重要的一部分就是将表型(phenotype)和基因组(genotype)相关联，也就是基因定位。在基因组时代，找到一个基因组常用的方位为：GWA, bin-map, BSA^[QTL Mapping Using High-Throughput Sequencing], 涉及到基本原理在高中课本上都有：

1. 基因在染色体上线性排布
1. 染色体会发生重组和交换

其中BSA在三者中比较简单，基本原理用1991年的文献^[Identification of markers linked to disease-resistance genes by bulked segregant analysis: a rapid method to detect markers in specific genomic regions by using segregating populations.]的胶图就能解释清楚。但是你需要提供恰当的群体送样测序，不然会给造成后续的数据分析无法进行，所谓garbage in, garbage out.
![](http://oex750gzt.bkt.clouddn.com/17-10-20/40048808.jpg)

## 流程介绍

BSA-Seq 在正向遗传学定位时也称之为Mapping-by-Sequencing, 分为如下5步：

1. 物理或化学(EMS)诱变
1. 群体构建方案选择：outcross 或 backcross(植物群体优先考虑)
1. 挑选目标表型的群体
1. 上机测序
1. 数据分析

![](http://oex750gzt.bkt.clouddn.com/17-10-20/53782275.jpg)

我们主要关心的是数据分析这一步，可以继续分为：

1. 数据清洗, 过滤掉不合格的reads
1. 序列比对, 常用软件为BWA, Bowtie2
1. SNP Calling, 常用文件为SAMTool+BCFTools, GATK, Freebayes
1. 通过作图确定可能区域
1. 变异位点注释

**注**: 尽管我们主要负责数据分析，但是实验设计这一步就需要介入，避免产生垃圾数据。

### 建立项目

在正式开始分析之前，需要预先构思项目结构，才能有序存放数据，保证数据的可重复性和鲁棒性(robust).

```shell
mkdir -p BSA_pipeline/{data/seq, scripts, analysis}
# {A,B} : 同时表示A和B
# [AB] : A或B
# ? : 任意单个字符
# * : 任意多个字符
```

项目存放在BSA_pipeline, 测序数据存放项目下的data/seq中，分析脚本则是scripts，分析得到的结果则是在analysis.

### 数据获取和清洗

数据由[SHOREmap](http://bioinfo.mpipz.mpg.de/shoremap/examples.html)提供，分为outcross analysis 和 backcross analysis 两类数据，我们仅需要下载回交分析所需要的数据即可，通过`wget`或`curl`等Linux命令行下载工具。

```shell
# 当前所在位置为项目文件下
cd data/seq
wget -q http://bioinfo.mpipz.mpg.de/shoremap/data/software/BC.fg.reads1.fq.gz & wget -q http://bioinfo.mpipz.mpg.de/shoremap/data/software/BC.fg.reads2.fq.gz & wget -q http://bioinfo.mpipz.mpg.de/shoremap/data/software/BC.bg.reads1.fq.gz &
wget -q http://bioinfo.mpipz.mpg.de/shoremap/data/software/BC.bg.reads2.fq.gz &
```

随后使用FastQC查看一下数据质量

```shell
ls | xargs -i fastqc {}
multiqc .
mv *html *zip multiqc_data ../analysis
```

![](http://oex750gzt.bkt.clouddn.com/17-10-20/40194262.jpg)

这个数据质量就不需要做过多的QC了。质量控制只能锦上添花，而不是起死回生。

### 序列比对

接着分别将亲本和BCF~2~比对到参考基因组拟南芥上。

> 高通量测序之后会得到海量的数据，对比对就提出很大的难题。尽管传统的BLAST也能将这些短序列比对到参考基因组上，但是时间成本太大，于是就开发了一些列基于 Burrows-Wheeler transformation的比对软件。其中BWA由Heng Li开发，是目前使用最普遍的软件之一。

```shell
# 项目根目录下
reference=~/Genome/Athalina/TAIR10/BWA/TAIR10.fa #reference根据实际情况
bwa mem -t 5 -M $reference BC.bg.reads1.fq.gz BC.bg.reads2.fq.gz | samtools sort -  -o  data/alignment/BC_bg_sort.bam
samtools index data/alignment/BC_bg_sort.bam
bwa mm -t 5 -M $reference BC.fg.reads1.fq.gz BC.fg.reads2.fq.gz | samtools sort -  -o  data/alignment/BC_fg_sort.bam
samtools index data/alignment/BC_fg_sort.bam
```

比对结果可用IGV进行可视化, 但这一步对于目前而言不是非常必要的, 在后续变异位点的确认上才比较重要。

### SNP Calling

SNP Calling的基本原理是利用贝叶斯定律:

> 如果比对后的短序列和参考基因组存在差异，也就是一个候选的variant, 它既有可能是测序不准造成，也可能是比对软件的精确性不够导致，甚至可能是抽样误差。为了确定他是不是一个真实的变异位点，可以通过先验概率进行判断。比如说一共由30条read比对到这个位点上，其中有20条read都和参考基因组不同，那么很大概率上他就是一个真实变异点。
![](http://oex750gzt.bkt.clouddn.com/17-10-20/70857485.jpg)

目前在人类中，最好用的Varint Calling软件是GATK，植物中也可以用，但是samtools mpipeup + bcftools 基本满足需求，也是Li用C开发的程序，运行效率高。

```shell
samtools mpileup -vu -t AD,DP -f $reference data/alignment/BC_bg_sort.bam |\
bcftools call -vm -Ov > data/variant/BC_bg_raw_variant.vcf &
samtools mpileup -vu -t AD,DP -f $reference data/alignment/BC_fg_sort.bam |\
bcftools call -vm -Ov > data/variant/BC_fg_raw_variant.vcf &
```

### 确定候选区域

确定候选区域一般都是采用作图法，寻找由于连锁形成的peak。这一步可以借由SHOREmap完成， 也可通过R和Python进行作图，能作为R和Python编程练习.

当然SHOREmap的安装就需要用到上午Linux基础学习的软件安装知识。

> 这里涉及到一个新的概念叫做动态编译库，因为安装dislin需要用到libXm.so.4

```shell
# 前置
sudo apt-get install build-essential
sudo apt-get install libXm4
# 安装dislin科学库
## 下载解压缩
cd ~/src
wget ftp://ftp.gwdg.de/pub/grafik/dislin/linux/i586_64/dislin-11.0.linux.i586_64.tar.gz
tar -zxvf dislin-11.0.linux.i586_64.tar.gz
cd dislin-11.0
## 设置安装路径
mkdir -p $HOME/biosoft/dislin
DISLIN=$HOME/biosoft/dislin
export DISLIN
## 安装
./INSTALL
## 复制dislin_d.h 到dislin的文件下
cp example/dislin_d.h $DISLIN
# 安装SHOREmap
cd ~/src
wget http://bioinfo.mpipz.mpg.de/shoremap/SHOREmap_v3.5.tar.gz
tar -zxvf SHOREmap_v3.5.tar.gz
# 替换SHOREmap下的dislin的一些文件
cd SHOREmap_v3.5
rm dislin/*dislin_d.*
cp $DISLIN/*dislin_d.* dislin
## 在配置文件中增加动态链接库
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/src/SHOREmap_v3.4/dislin
## 到之前安装的文件夹下
cd ~/src/SHOREmap_v3.5
make
## 将编译文件拷贝到习惯的文件夹中
mkdir ~/bin
mv SHOREmap ~/bin
```

如果你可以独立编译出SHOREmap，你的Linux基础已经过关了.因为这个过程涉及到软件下载，解压缩，环境变量，动态编译库，程序运行等。

虽然软件的编译很复杂，但是使用却非常的简单：

- SHOREmap convert: 格式转换

```shell
~/bin/SHOREmap convert --marker bc_bg_raw_variant.vcf --folder Background -runid 1
~/bin/SHOREmap convert --marker bc_fg_raw_variant.vcf --folder Foreground -runid 1
```

- SHOREmap extract: 提取候选分子标记

```shell
mkdir SHOREmap_analysis
# Background
~/bin/SHOREmap extract --chrsizes chrSizes.txt --folder SHOREmap_analysis/background --marker Background/1_converted_variant.txt --consen Background/1_converted_consen.txt -verbose
# Foreground
~/bin/SHOREmap extract --chrsizes chrSizes.txt --folder SHOREmap_analysis/foreground/ --marker Foreground/1_converted_variant.txt --consen Foreground/1_converted_consen.txt -verbose
```

- SHOREmap backcross: 回交分析

```shell
SHOREmap backcross --chrsizes chrSizes.txt --marker Foreground/1_converted_variant.txt \
--consen SHOREmap_analysis/foreground/extracted_consensus_0.txt \
--folder SHOREmap_analysis/BC_analysis -plot-bc --marker-score 40 \
--marker-freq 0.0 --min-coverage 10 --max-coverage 80 \
-bg Background/1_converted_variant.txt --bg-cov 1 --bg-freq 0.0 \
--bg-score 1 --non-EMS --cluster 1 --marker-hit 1 -verbose
```

会生成每一条染色体等位基因频率分布的PDF文件, 这一步就非常考验你的判断能力，找到那些可能的区域.

![](http://oex750gzt.bkt.clouddn.com/17-10-20/73114882.jpg)

- SHOREmap annotate: 注释候选区域

```shell
~/bin/SHOREmap annotate --chrsizes chrSizes.txt \
--folder SHOREmap_analysis/BC_analysis/ \
--snp Foreground/1_converted_variant.txt \
--chrom Chr3 --start 1 --end 4000000 --genome ../TAIR10.fa \
--gff ../TAIR10_GFF3_genes.gff
```

查看AF大于0.8的位点，即可使用IGV查看对应位点：也可用直接搜索对应的注释数据库

![](http://oex750gzt.bkt.clouddn.com/17-10-20/28526430.jpg)

- 查看详细的注释信息

```shell
cat SHOREmap_analysis/BC_analysis/prioritized_snp_Chr3_1_4000000_peak1.txt  |\
awk '$6 > 0.8 {print $p}' | cut -f 10 | xargs -i grep {} ../TAIR10_functional_descriptions
```

根据表型和注释信息相互验证，确定`AT3G05040.1`是候选基因，

>AT3G05040.1	protein_coding	ARM repeat superfamily protein	Encodes member of importin/exportin family. Involved in timing of shoot maturation. Involved in miRNA transport. Mutants flower early and have small, curled leaves and reduced abundance of certain miRNA species.	HASTY (HST); CONTAINS InterPro DOMAIN/s: Exportin-1/Importin-beta-like (InterPro:IPR013598), Armadillo-type fold (InterPro:IPR016024); Has 520 Blast hits to 417 proteins in 141 species: Archae - 0; Bacteria - 0; Metazoa - 205; Fungi - 183; Plants - 102; Viruses - 0; Other Eukaryotes - 30 (source: NCBI BLink).

## 推荐阅读

所有推荐资料存放在推荐阅读中

- Plant Functional Genomics
- Using next-generation sequencing to isolate mutant genes from forward genetic screens