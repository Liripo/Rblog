---
title: Rcpp
date: "2020-03-28"
tags: "R"
---

# RCpp与R包

首先在生成`R`骨架的时候，比一般没调用`C++`多执行一下`Rscript -e 'usethis::use_rcpp()'`。然后在`.gitignore`文件中添加`src/*.o`,`src/*.so`,`src/*.dll`防止编译文件导入到`git`中。并且在`DESCRIPTION`文件中必须添加`LinkingTo: Rcpp`, `Imports: Rcpp`才能识别`Rcpp`库。

接着就是添加两个`roxygen`到代码中即可。参考如下：

```
#' @useDynLib Rcpp文件名
#' @importFrom Rcpp sourceCpp
NULL
```



# 计算矩阵欧式距离

```R
Rcpp::cppFunction("
    NumericMatrix dist_crpp (NumericMatrix x){
        int row = x.nrow();
        double d;
        NumericMatrix out (row,row);
        for (int i = 0;i < row;i++){
            for (int j = i+1;j < row;j++){
                NumericVector v1 = x.row(i);
                NumericVector v2 = x.row(j);
                NumericVector v3 = v2 - v1;
                d = sqrt(sum(pow(v3,2)));
                out(j,i) = d;
                //out(i,j) = d;
            }
        }
        return (out);
    }             
")
```

