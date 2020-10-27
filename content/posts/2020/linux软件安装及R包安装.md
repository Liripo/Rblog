---
title: linux软件安装及R、R包安装
date: "2020-03-28"
tags: "linux"
toc: true
---
# apt卸载软件
内容如下：
- 卸载软件：sudo apt-get remove 软件名(+版本号)
- 卸载软件并删除依赖：sudo apt-get remove --auto-remove 软件名(+版本号)
- 清除软件：sudo apt-get purge 软件名(+版本号)
- 清除软件及依赖：sudo apt-get purge --auto-remove 软件名(+版本号)

# conda

现在清华镜像源一直没更新最新版本的R让我很无奈，所以我清除了conda的清华镜像源，选择[conda-forge](https://conda-forge.org/),具体可以看它官网首页就有介绍怎么使用了。当然，使用它可能还取决于你的网络哦。

```shell
conda search r-base
conda install r-base == 4.0.2
```

即可。

## R包更新

更新版本后，R包必须全部更新

```R
install.package("rvcheck")
rvcheck::uodate_all()
```

