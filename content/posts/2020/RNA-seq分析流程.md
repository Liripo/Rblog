---
title: RNA-seq分析流程
date: 2020-10-18
tags: bio
toc: true
---

>一般已发表文章所包含的数据可以在NCBI (SRA、GEO 等)、EMBL-EBI 等相关数据库获得。

[ncbi数据下载参考](https://liripo.github.io/post/ncbi%E6%95%B0%E6%8D%AE%E4%B8%8B%E8%BD%BD/)

[高通量测序知识](https://shixiangwang.github.io/home/cn/post/2017-10-27-rnaseq-data-analysis/)

下载测序数据后，可以进行质控(fastqc等)，比对(bwa,STAT,subread等，非常多)，获取counts数，之后差异分析，GO，KEGG等。当然还有call snp等。

# 质控

[fastqc使用](https://liripo.github.io/post/fastqc%E4%BD%BF%E7%94%A8/)，相对应的R包[fastqcr](http://www.sthda.com/english/wiki/fastqcr-an-r-package-facilitating-quality-controls-of-sequencing-data-for-large-numbers-of-samples)，

[rqc](https://bioconductor.org/packages/release/bioc/html/Rqc.html)

[fastp](https://liripo.github.io/post/fastp-gencore%E4%BD%BF%E7%94%A8/)

Biostrings包计算GC含量，Q20等

```R
library(Biostrings)
filepath <- system.file("extdata", "s_1_sequence.txt",
                        package="Biostrings")
qdna2 <- readQualityScaledDNAStringSet(filepath)
qdna2
#得到每一行的GC含量
GC_content <- letterFrequency(DNAStringSet(qdna2), letters="CG",as.prob = T)
#得到整个fastq的GC含量
letterFrequency(DNAStringSet(qdna2), letters="CG",as.prob = T,collapse = T)
#Q20，将质量分数转为数值去计算,整个文件
qa <- as(quality(qdna2),"IntegerList")
library(dplyr)
sum_20 <- purrr::map_int(seq_along(qa),~length(which(qa[[.x]]>20)) %>% 
	sum()
sum_all <- quality(qdna2) %>% width() %>% sum()
q20 <- sum_20/sum_all
```

# 比对

在比对文件之前需要先要去掉adapter，得到不含有adapter 的fastq 文件。有一个flexbark可以，

```shell
FLEXBAR -t 输出文件名 -qf i1.8 -r 输入文件 -a Adapters.fa
```

我没试过去接头。

> 反正有了trimmed 数据后，就要与参考文件进行比对。目前大家所认同的比对算法
> 有很多，比如Bowtie2，BWA，TopHat，HISAT2 和STAR 等等。试下
> 综合指标 (Sahraeian 等，2017) 比较高的STAR 进行比对。

比对大部分需要建立索引,加快速度。代码

```shell
STAR --runThreadN 线程数 --runMode genomeGenerate
--genomeDir 存放参考基因的目录 --genomeFastaFiles 参考基因组的
fasta 文件 --sjdbGTFfile 参考基因组的gtf 文件 --sjdbOverhang
索引长度,这个是reads长度的最大值减1，默认是100
```

建立好索引就可以对数据进行比对了，比对如下：

```shell
STAR --genomeDir 存放参考基因的目录 --readFilesIn 输入文件(read1,if need;read2)
--runThreadN 线程数 --outSAMtype BAM --outFileNamePrefix 输
出文件前缀
```

具体可以参考[https://www.bioinfo-scrounger.com/archives/288/](https://www.bioinfo-scrounger.com/archives/288/)

# counts数

- bedtools multicov

[bed文件从gtf的获取](https://liripo.github.io/post/%E7%94%9F%E4%BF%A1%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F%E6%B1%87%E6%80%BB/#gtf/gff3/GenePred)

```shell
bedtools multicov -bams *.bam -bed bed文件 >reads_by_bed.txt
```

虽然-bams可以使用通配符，但是我一般还是用shell写脚本循环，因为输出文件没有列名，很容易混淆的。我认为输出文件最好能带个列名。

- **[featureCounts](http://bioinformatics.oxfordjournals.org/cgi/reprint/btt656?ijkey=ZzPz96t2lqzAH6F&keytype=ref)**

 [user guide](http://bioinf.wehi.edu.au/featureCounts/)

比如双端测序，

```shell
featureCounts -p -t gene -g gene_name -a annotation.gtf -o counts.txt *_mapping__PE.bam
```

直接使用gene_name做标识符也行,提取出gene的位置。

类似的还有Htseq-count

而samtools 的bedcov计算的则不同，是bed文件每个区域的深度和。即samtools depth的和。

而计算覆盖到整个参考fasta可以用samtools flagstat或者更详细的samtools idxstats。虽然featureCounts等也会给出，不过是输出在标准输出里的，不在输出文件里。

## TPM等转化

reads数即counts数，接着如果需要比较不同样本同个基因上的表达丰度情况，则需要对count数进行标准化。

> 落在一个基因区域内的read counts数目一般可以认为取决于length of the gene（基因长度）和sequencing depth（测序深度）

- RPKM和FPKM。前者是以每个reads作为一个单位，在单端测序中应用较多；而后者是以fragment作为一个单位，主要应用在双端测序后的分析。
- 最近几年开始应用TPM（Transcripts Per Million）较多，Transcripts Per Kilobase of exonmodel per Million mapped reads (即是先对reads进行基因长度矫正，然后再除以所有矫正后的reads的总和)，认为优化的RPKM计算方法，可以用于同一物种不同组织的比较。

>FPKM：Fragments Per Kilobase of transcript per Million fragments mapped，每1百万个map上的reads中map到map到外显子的每1k个碱基上的fragments个数。如果一对paired reads都比对上了，那么这对reads当做一个fragments；如果paired reads中一个比对上，另外一个没比对上，那么比对上的那个reads当做一个fragments。

```shell
FPKM=read counts/(mapped reads (Millions) * exon length(KB))
```

所以，这个fragments是不是featureCounts算出的counts数我不太确定。反正rpkm肯定是。

在R中，则

```R
counts_to_fpkm <- function(counts,lengths){
    rates <- counts/lengths
    fpkm <- rates /sum(counts) * 1e9
}
```

而TPM则是

```R
counts_to_tpm <- function(counts,lengths){
    rates <- counts/lengths
    tpm <- rates/sum(rates) * 10^6
}
```

有人认为是clean reads，也就是比对前的clean reads总数【我觉得没必要】，但大多数人还是定义为有效的reads，即mapped reads。那么，mapped reads究竟是哪个值呢，因为比对到gene,exon的总mapped reads不等于map到fasta的reads吧，毕竟肯定有内含子之类的。这里我觉得既然是标准化，就应该是比如feauture得到的那些数据去标准就好，所以比值应该是比对到gene,exon的总mapped reads【我认为】。

那么，exon length呢，粗暴地理解为gene length？【这样方便获取】，正常来说应该是每个gene id的exon的长度总和。获取所有外显子的长度和呢，一般可以从gtf文件入手，但是gtf文件中的exon的区域很多都是重合的（不同转录本的可变剪切）【比较麻烦。】

当然，使用featureCounts时，可以

```shell
featureCounts -p -t exon -g gene_id -a annotation.gtf -o counts.txt *_mapping__PE.bam
```

输出文件有一列length，即为exon的length,方便。

而在R中,可以使用GenomicFeatures计算每个基因下所有外显子的总长度

```R
#制作txdb对象
library(GenomicFeatures)
txdb <- makeTxDbFromGFF("hg38.gtf",format="gtf")
#通过exonsBy获取每个gene上的所有外显子的起始位点和终止位点，然后用reduce去除掉重叠冗余的部分，最后计算长度
exons_gene <- exonsBy(txdb, by = "gene")
exons_gene_lens <- purrr::map_int(exons_gene,~sum(width(reduce(.x))))
```

# 差异分析

差异分析使用的是counts文件，相关性分析之类的可以使用TPM数据。

# call snp

