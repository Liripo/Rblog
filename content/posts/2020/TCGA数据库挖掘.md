---
title: TCGA
date: "2020-03-28"
tags: "bio"
summary: " "
---
# TCGA样本id解析

如：`TCGA-75-5122-01A-01R-1755-07`,ID

- TCGA：Project, 所有TCGA样本名均以这个开头，标志。

- 75：表示肺腺癌，具体对应编码在[https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/tissue-source-site-codes](https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/tissue-source-site-codes)

- 5112:Participant, 参与者编号

- ==01：Sample, 这两个数字可以说是最关键、最被大家注意的，其中编号01\~09表示肿瘤，其他代码表示正常对照，具体如[https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/sample-type-codes](https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/sample-type-codes),在TCGA样本名中，这个位置最常见的就是01和11，当然偶尔也会有其他的数字==

- A：Vial, 在一系列患者组织中的顺序，绝大多数样本该位置编码都是A; 很少数的是B，表示福尔马林固定石蜡包埋组织，已被证明用于测序分析的效果不佳，所以不建议使用-01B的样本数据。

  区分不同样本。

- 01,portion，同属于一个患者组织的不同部分的顺序编号，Order of portion in a sequence of 100 - 120 mg sample portions，01为The first portion of the sample，一般值为01-99.

- R：分析分子类型，R为RNA，具体如：[https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/portion-analyte-codes](https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/portion-analyte-codes)

- 1755:Plate, 在一系列96孔板中的顺序，值大表示制板越晚

- 07：Center, 测序或鉴定中心编码，更多编码详见:[https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/center-codes](https://gdc.cancer.gov/resources-tcga-users/tcga-code-tables/center-codes)
# 数据下载

