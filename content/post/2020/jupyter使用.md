---
title: "jupyter使用"
author: "Liripo"
date: "2020-07-20"
colorPage: "#252D3E"
colorText: "#FFFFFF"
colorFade: "#888888"
colorAccent1: "#4A5570"
colorAccent2: "#FFCACA"
header:
  image: 'header/custom_banner.png'
  profile: 'header/custom_profile.png'
toc: true
---

# 安装
可以查看[官网](https://jupyter.org/)
之后使用`jupyter notebook`即可运行

# 修改工作目录
没有jupyter配置文件需要生成一个初始的配置文件
```R
 jupyter notebook --generate-config
```
 之后会在命令行生成一个配置文件：`"C:\Users\Administrator\.jupyter\jupyter_notebook_config.py"`

 查找`The directory to use for notebooks and kernels`字样
 修改#c.NotebookApp.notebook_dir = “”为所要的工作目录，记得去掉“#”号。

 # 快捷键
jupyter的快捷键帮助是中文的，可以直接看Help.

# jupyter使用R
网址[github](https://github.com/IRkernel/IRkernel)

# 使用多个python版本

在其他python路径中安装ipykernel,第一个是你的其他python版本的具体路径
```shell
python3.7.exe -m pip install ipykernel
```
然后运行
```shell
python3.7.exe -m ipykernel install -h
```
查看帮助，发现有个--user 和--name --display-name参数可以使用
所以接着运行
```shell
python3.7.exe -m ipykernel install --user --name python3.7 --display-name python3.7
```
之后运行
```shell
jupyter kernelspec list
```
应该会看到你的用户目录多了个文件夹,比如我的,因为我还装了R，所以有个ir
```shell
Available kernels:
  python3      C:\Users\Liripo\AppData\Roaming\jupyter\kernels\python3
  python3.7    C:\Users\Liripo\AppData\Roaming\jupyter\kernels\python3.7
  ir           C:\ProgramData\jupyter\kernels\ir
```

# linux下安装jupyterlab
【为什么我执着于conda与源码编译，直接下载二进制版本,单纯只是因为我没有sudo权限。】

```shell
conda install -c conda-forge jupyterlab
```
顺便的jupyter notebook也装上了。
```shell
jupyter notebook --version
```
## 配置
```shell
jupyter lab --generate-config
```

```shell
## Writing default config to: /home/liripo/.jupyter/jupyter_notebook_config.py
```

如上所述，修改默认工作区。

jupyter使用R,

1.R中下载包：

```R
install.packages('IRkernel')
```

2.注册内核

```shell
# in R 4.0.2
IRkernel::installspec(name = 'ir402', displayname = 'R4.0.2')
```

使用Rstuio快捷键

在运行命令前，需要装好node.js和npm，可以在[node官网](https://nodejs.org/en/download/)下载二进制版本，解压后配置环境变量。之后bash下运行：

```
jupyter labextension install @techrah/text-shortcuts 
```

```shell
jupyter kernelspec list
```

`jupyter kernelspec list`看下是否有R内核。

# 使用

在使用jupyter写了一个脚本后，例如保存为`Untitled.ipynb`，使用命令转换为R脚本。

```
jupyter nbconvert --to script Untitled.ipynb
```

## jupyterlab绘图使用中文

```shell
wget https://github.com/welai/glow-sans/releases/download/v0.9/GlowSansSC-Normal-v0.9.zip ~/.fonts
unzip GlowSansSC-Normal-v0.9.zip
```

```
#查看下载的字体
fc-list
#查看支持的中文字体
fc-list :lang=zh
```

下载好后会看到中文支持字体。
```shell
/home/liripo/.fonts/GlowSansSC-Normal-ExtraBold.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal ExtraBold,ExtraBold
/home/liripo/.fonts/GlowSansSC-Normal-Heavy.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Heavy,Heavy
/home/liripo/.fonts/GlowSansSC-Normal-ExtraLight.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal ExtraLight,ExtraLight
/home/liripo/.fonts/GlowSansSC-Normal-Regular.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Regular,Regular
/home/liripo/.fonts/GlowSansSC-Normal-Thin.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Thin,Thin
/home/liripo/.fonts/GlowSansSC-Normal-Bold.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Bold,Bold
/home/liripo/.fonts/GlowSansSC-Normal-Light.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Light,Light
/home/liripo/.fonts/GlowSansSC-Normal-Book.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Book,Book
/home/liripo/.fonts/GlowSansSC-Normal-Medium.otf: Glow Sans SC,未来荧黑,Glow Sans SC Normal,未来荧黑 Normal:style=Normal Medium,Medium
```

生成缓存,快速在程序中使用

```shell
fc-cache
```

重启jupyter lab即可.