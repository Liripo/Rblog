---
title: "blast学习"
author: "Liripo"
date: '2020-08-02'
slug: NCBI
tags: bio
toc: true
---
最新的blast可以在页面[ftp://ftp.ncbi.nih.gov/blast/executables/LATEST/](ftp://ftp.ncbi.nih.gov/blast/executables/LATEST/)找到。

# 本地使用

[blast手册页](https://www.ncbi.nlm.nih.gov/books/NBK279668/)

[blast命令行](https://www.ncbi.nlm.nih.gov/books/NBK279684/#appendices.Options_for_the_commandline_a)

blastn使用,blastn -task有四个选项，分别为

- blastn

- blastn-short

- megablast

- dc-megablast

具体可以在手册页找到。
一开始应该建库，
比如xxx.fasta核苷酸序列建库,可以简单
```shell
makeblastdb -in xxx.fasta -dbtype nucl
```
makeblastdb参数
```shell
-in 需要建库的fasta序列
 -dbtype 如果是蛋白库则用prot，核酸库用nucl
 -out 所建数据库的名称
 -parse_seqids => Parse bar delimited sequence identifiers (e.g., gi|129295) in FASTA input.
 -hash_index => Create index of sequence hash values.
-out =>Name of BLAST database to be created. Input file name is used if none provided. This field is required if input consists of multiple files.
```
更加具体的可以看[blast命令行](https://www.ncbi.nlm.nih.gov/books/NBK279684/#appendices.Options_for_the_commandline_a)

比对的话我喜欢借鉴metablastr(R包)的输出，在命令行使用

```shell
blastn -query xxx.fasta \
    -db reference.fasta \
    -num_threads 30 \
    -task megablast \
    -outfmt "10 qseqid sseqid pident nident length mismatch gapopen gaps positive ppos qstart qend qlen qcovs qcovhsp sstart send slen evalue bitscore score" >xxx.csv
```

之后使用R给输出加上表头

```R
blast <- metablastr::read_blast(xxx.csv,out.format = "csv")
```

默认的-outfmt是0。以比对的形式展示。

SRA数据下载后，解压后得到fastq文件，可以转为fasta之后进行megablast之类的，当然，也可以在NCBI上操作，只是输出结果有5000条的限制。

或者有一个[https://ncbi.github.io/magicblast/](https://ncbi.github.io/magicblast/)可以试试直接分析sra数据。

