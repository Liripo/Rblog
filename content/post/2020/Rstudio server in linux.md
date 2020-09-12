---
title: Rstudio server in linux
date: "2020-09-06"
author: Liripo
tags: "linux"
---

# 安装

[官网教程](https://rstudio.com/products/rstudio/download-server/debian-ubuntu/)

由于刚刚升级了WSL2[^4],所以就尝试下WSL2安装Rstudio server。

首先你要先确定自己是否安装了R，其中conda怎么安装最新版本的R的笔记我也写过。

```shell
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.3.1073-amd64.deb
sudo gdebi rstudio-server-1.3.1073-amd64.deb
```

```shell
Reading package lists... Done
Building dependency tree
Reading state information... Done
Reading state information... Done

RStudio Server
 RStudio is a set of integrated tools designed to help you be more productive with R. It includes a console, syntax-highlighting editor that supports direct code execution, as well as tools for plotting, history, and workspace management.
Do you want to install the software package? [y/N]:y
(Reading database ... 51259 files and directories currently installed.)
Preparing to unpack rstudio-server-1.3.1073-amd64.deb ...
Unpacking rstudio-server (1.3.1073) over (1.3.1073) ...
Couldn't find an alternative telinit implementation to spawn.
Setting up rstudio-server (1.3.1073) ...
useradd: user 'rstudio-server' already exists
Couldn't find an alternative telinit implementation to spawn.
```

你最后会看到这条信息，按照官网文章[^1]所述，`Couldn't find an alternative telinit implementation to spawn.`可以忽略。

```shell
sudo rstudio-server start
```

开启服务，即可在[http://localhost:8787](http://localhost:8787/)打开。

接着即可以ssh的用户名密码登录。

- 停止服务

  ```shell
  sudo rstudio-server stop
  ```
  
- 卸载服务

  ```shell
  sudo apt-get remove rstudio-server -y
  ```
- 升级版本[^3]
  ```shell
  #检查是否正在运行任何活动会话
  sudo rstudio-server active-sessions
  #暂停所有活动用户会话,可以跳过
  sudo rstudio-server suspend-all
  #将旧版本的服务器置于离线模式
  sudo rstudio-server offline
  #安装新版本，即按照之前操作，例如
  wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-XXX-amd64.deb
  sudo gdebi rstudio-server-XXX-amd64.deb
  #重新启动服务器，新版本的服务器将以离线模式启动。
  sudo rstudio-server restart
  #恢复服务为在线状态
  sudo rstudio-server online
  ```
# 使用

1.检查是否在运行

```shell
ps -aux|grep rstudio-server
```

2.指定R版本，例如`nvim /etc/rstudio/rserver.conf`

```shell
#指定R
rsession-which-r=/usr/local/bin/R
#共享库
rsession-ld-library-path=/opt/someapp/lib:/opt/anotherapp/lib
```



官网文档[^2]

[^1]: https://support.rstudio.com/hc/en-us/articles/360049776974-Using-RStudio-Server-in-Windows-WSL2
[^2]: https://docs.rstudio.com/ide/server-pro/index.html
[^3]: https://support.rstudio.com/hc/en-us/articles/216079967-Upgrading-RStudio-Server
[^4]: https://liripo.github.io/post/wsl%E6%9B%B4%E6%96%B0%E5%88%B0wsl2/