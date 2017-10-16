## 一个模拟项目

```
## 源代码方式安装 bwa-0.7.15 
## conda安装samtools
cd tmp/chrX_Y/hg19/
wget http://hgdownload.cse.ucsc.edu/goldenPath/hg19/chromosomes/chrX.fa.gz  
wget http://hgdownload.cse.ucsc.edu/goldenPath/hg19/chromosomes/chrY.fa.gz 
gunzip chrX.fa.gz
gunzip chrY.fa.gz
~/biosoft/bwa/bwa-0.7.15/bwa index chrX.fa
perl simulate.pl chrY.fa ## 这个perl脚本在 http://www.bio-info-trainee.com/wp-content/uploads/2015/10/tmp.png 
~/biosoft/bwa/bwa-0.7.15/bwa mem -t 5 -M chrX.fa read*.fa >read.sam
samtools view -bS read.sam >read.bam
samtools flagstat read.bam
samtools sort -@ 5 -o read.sorted.bam read.bam
samtools view -h -F4 -q 5 read.sorted.bam |samtools view -bS|samtools rmdup - read.filter.rmdup.bam
samtools index read.filter.rmdup.bam
samtools mpileup -ugf ~/tmp/chrX_Y/hg19/chrX.fa read.filter.rmdup.bam |bcftools call -vmO z -o read.bcftools.vcf.gz
## 把fa/bam/vcf 载入到 IGV 进行可视化，截图其中一个变异位点
## 参考 http://www.biotrainee.com/thread-696-1-1.html
```

## 一个实战项目


