---
title: NCBI数据下载
date: "2020-10-13"
tags: bio
---


> NCBI共享大量数据。 2020年，PubMed中有3160万篇论文，其中PubMed Central中有660万篇全文记录。 NCBI核苷酸数据库（包括GenBank）具有4.307亿个不同序列的数据，而dbSNP描述了7.206亿个不同的遗传变异。 所有这些记录都可以与NCBI分类中的186万个物种或OMIM中的2.69万个疾病相关的记录进行交叉引用。

# SRA数据下载

## SRA toolkit

参考文章；[https://www.ncbi.nlm.nih.gov/sra/docs/sradownload/](https://www.ncbi.nlm.nih.gov/sra/docs/sradownload/)

文章中有特别强调要用最新的sra toolkit(时间2020-10-13)，所以目前你用aspera估计不能下了。另一种是直接找到链接直接浏览器下载或者wget。

sra toolkit下载地址：[https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit](https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit)

下载完解压后需要配置。配置看[https://github.com/ncbi/sra-tools/wiki/03.-Quick-Toolkit-Configuration](https://github.com/ncbi/sra-tools/wiki/03.-Quick-Toolkit-Configuration)

只有一个run时，直接运行

```shell
prefetch SRR******
```

一个SraAccList.txt文件，如：

```shell
SRR11192680
SRR11192681
SRR11192682
SRR11192683
SRR11192684
```

使用命令：

```shell
prefetch --option-file SraAccList.txt
```

下完后解压sra格式文件。可以使用**fasterq-dump** and **sam-dump**

```shell
fasterq-dump --split-files SRR11180057.sra
```

# rentrez

在R里面运行entrez。

> Entrez是分子生物学数据库系统，可提供对核苷酸和蛋白质序列数据，以基因为中心和基因组作图的信息，3D结构数据，PubMed MEDLINE等的集成访问。该系统由国家生物技术信息中心（NCBI）搭建。

下载核酸序列，比如sars-cov-2

```R
fasta <- entrez_fetch(db = "nucleotide",id = "NC_045512.2" ,rettype = "fasta")
write_lines(fastas," NC_045512v2.fasta")
```

还有更多使用参考文章：[https://docs.ropensci.org/rentrez/articles/rentrez_tutorial.html](https://docs.ropensci.org/rentrez/articles/rentrez_tutorial.html)