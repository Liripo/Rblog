---
title: wsl更新到WSL2
date: "2020-09-05"
tags: "linux"
toc: true
---

# 更新

[官网教程](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)

- 首先检查自己的windows版本是否符和需求，

  cmd键入`winver`，可以看到你的版本。

  ![wsl2-1](https://liripo.github.io/image/wsl2-1.png)

`systeminfo`可以看下系统类型，在安装wsl2内核时需要注意，不过问题不大。

![image-20200905135603281](https://liripo.github.io/image/wsl2-2.png)

系统符合要求的话，如果你装过了WSL的话，可以直接在powershell输入,

记住，这儿请以管理员权限打开，之后操作都在管理员权限下操作。

```shell
 wsl -l -v
```

![image-20200905140124064](https://liripo.github.io/image/wsl2-3.png)

没有装过的话，参考：[https://liripo.github.io/post/windows10%E5%AE%89%E8%A3%85linux/](https://liripo.github.io/post/windows10%E5%AE%89%E8%A3%85linux/)

这个图片这儿请假想为1,因为我已经是更新好后在写教程了。

你的wsl程序如果只能用wsl1的话，是不会有-l参数的，所以在这里你也可以判断是不是能更新到wsl2，不能的话得获取符合要求的系统。

之后输入

```shell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

```shell
wsl --set-default-version 2
```

加入这儿遇到错误0x1bc的话，参考：[https://github.com/microsoft/WSL/issues/5651](https://github.com/microsoft/WSL/issues/5651),也就是需要右键点击wsl2内核进行卸载，再重新设置。

之后输入：

```shell
wsl --set-version <distribution name> <versionNumber>
```

上述命令以我的Ubuntu-18.04为例，

```shell
wsl --set-version Ubuntu-18.04 2
```

wsl2则会自动安装，否则可以按照官网文档所示，手动下载。

最后键入

```shell
wsl -v -l
```

看看是否是WSL2。