---
title: Ka与Ks计算
date: "2020-09-03"
tags: "linux"
---

kaks的计算R包seqinr::kaks()可以计算，当然更多的使用KaKs_Calculator[^1]

软件获得在[http://evolution.genomics.org.cn/software.htm.](http://evolution.genomics.org.cn/software.htm.)

下载解压缩后可以在里面找到windows，Linux版本以及源代码，示例文件以及帮助文档。

>Ka/Ks，在遗传学中，**Ka/Ks**或者**dN/dS**表示的是异义替换（Ka）和同义替换（Ks）之间的比例。这个比例可以判断是否有选择压力作用于这个蛋白质编码基因。
>
>不导致氨基酸改变的[核苷酸](https://baike.baidu.com/item/核苷酸)变异我们称为同义突变，反之则称为非同义突变。一般认为，同义突变不受自然选择，而非同义突变则受到自然选择作用。在进化分析中，了解同义突变和非同义突变发生的速率是很有意义的。常用的参数有以下几种：同义突变频率(Ks)、非同义突变频率(Ka)、非同义突变率与同义突变率的比值(Ka/Ks)。如果Ka/Ks>1，则认为有正选择效应。如果Ka/Ks=1，则认为存在中性选择。如果Ka/Ks<1，则认为有纯化选择作用。

而且Ka,Ks不考虑终止子，比如stop gain,stop loss。

kaks_calculator使用的输入文件为axt文件

```shell
NP_000026
ATGCTCCTGTG-CCACTGGCC
ATCCCC-TGCGCTCACTGGAC
NP_000053
ACAGaTtCTACCc-GCCcACTA--GgtGtt
---ggTTCTCCtACCcA-G-CACTACTggg
```

就是一个基因或者一个开放阅读框有比对好的两条序列去计算。如果我们使用[mega](https://liripo.github.io/post/mega%E4%BD%BF%E7%94%A8%E5%8F%8A%E5%A4%9A%E5%BA%8F%E5%88%97%E6%AF%94%E5%AF%B9/)生成一个比对好的fasta文件后，

```R
>a
ATATTAGGTTTTTACCTACCCAGGAAAAGCCAACCAACCTCGATCTCTTGTAGATCTGTT
CTCTAAACGAACTTTAAAATCTGTGTAGCTGTCGCTCGGCTGCATGCCTAGTGCACCTAC
>b
----TAGGTTTTTACCTACCCAGGAAAAGCCAACCAACCTCGATCTCTTGTAGATCTGTT
CTCTAAACGAACTTTAAAATCTGTGTAGCTGTCGCTCGGCTGCATGCCTAGTGCACCTAC
```

就前面四个我改成空位了。

可以使用

fa_to_axt.R转下，我自己写的脚本。

```R
library(tidyverse)

args<-commandArgs(TRUE)
##比对后fasta,第一个参数
fasta <- args[1]
if(is.na(fasta)){stop("fasta文件必须提供!")}
#开放阅读框,第二个参数,可要可不要，不要就整个fasta文件转axt
orf <- args[2]

fas <- Biostrings::readDNAStringSet(fasta)

as_axt <- function(DNAStrings,name){
  chr <- DNAStrings %>% as.character()
  chr_write <- base::paste(chr,collapse =  "\n")
  res <- base::paste(name,chr_write,"",sep = "\n")
  res
}
seq_from_bed <- function(orf_tbl){
  map(1:nrow(orf_tbl),function(i){
    row <- orf_tbl[i,]
    orfname <- row[[1]]
    start <- row[[2]]
    end <- row[[3]]
    if((end - start + 1)%%3 != 0){stop("长度不是3的倍数!",call. = F)}
    subseq <- Biostrings::subseq(fas,start,end)
    name <- paste0(orfname,":",start,"-",end)
    axt <- as_axt(subseq,name = name)
    axt
  })
filename <- fs::path_file(fasta) %>% fs::path_ext_remove()
if(is.na(orf)){
  llsc <- as_axt(fas,name = filename)
}else{
  orf_tbl <- read_delim(orf,delim = "\t",col_names = F)
  llsc <- seq_from_bed(orf_tbl = orf_tbl)
}

llsc %>% write_lines(paste0(filename,".axt"))
```

使用,没有需要提取的对应位置的话，可以不传入开放阅读框的位置

```R
Rscript fa_to_axt.R xxx.fasta
```

如果你要提取出全基因序列某个能翻译蛋白质的序列区域，这用于多序列比对后的fasta文件，全基因组的可以用bedtools工具提取。

```R
Rscript fa_to_axt.R xxx.fasta xxx.bed
#xxx.bed如下
c	1	3
d	5	13
```

当然，更好的是有直接测得的两个基因的序列，直接进行比对然后转axt。但是要是其中一条序列你不知道开放阅读框位置，就得先全基因比对了吧。

```shell
KaKs_Calculator -i test.axt -o test.kaks -m MA
```

其中还有一个-c参数，寻找密码子类型的，默认标准密码子。

-m MA默认的这个最大似然方法十分耗时。可以试试跟-m NG的运行速度区别。

> 总之就是你必须获得两条比对好的序列，而且这序列是编码蛋白质的区域，然后可以自己写一个fasta文件转axt文件的程序或者用上面那个程序。

use LWL, YN and MYN and standard Code:

```shell
use LWL, YN and MYN and standard Code
KaKs_Calculator -i test.axt -o test.axt.kaks -m LWL -m YN -m MYN
```

方法可以多个，至于选哪种就不清楚了。

生成的结果：

● Ka: Nonsynonymous substitution rate
● Ks: Synonymous substitution rate
● Ka/Ks: Selective strength
● P-Value (Fisher): The value computed by Fisher exact test
● Length: Sequence length (after removing gaps and stop codon(s))
● S-Sites: Synonymous sites
● N-Sites: Nonsynonymous sites
● Fold-Sites (0:2:4): 0,2,4-fold degenerate sites
● Substitutions: Substitutions between sequences
● S-Substitutions: Synonymous substitutions
● N-Substitutions: Nonsynonymous substitutions
● Fold-S-Substitutions (0:2:4): Synonymous substitutions at 0,2,4-fold
● Fold-N-Substitutions (0:2:4): Nonsynonymous substitutions at 0,2,4-fold
● Divergence-Time: Divergence time

● Substitution-Rate-Ratio (rTC:rAG:rTA:rCG:rTG:rCA/rCA): Ratios of six substitution rates to the substitution rate between C and A
● GC(1:2:3): GC content of entire sequences and of three codon positions
● ML-Score: Maximum likelihood score
● AICc: Value of AICc
● Akaike-Weight: Value of Akaike weight for model selection
● Model: Selected model for the method of MS

这个软件还有绘图功能。详细看官方文档。

[^1]: KaKs_Calculator: Calculating Ka and Ks Through Model Selection and Model Averaging https://doi.org/10.1016/S1672-0229(07)60007-2

