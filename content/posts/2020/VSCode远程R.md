---
title: VSCode 远程使用
date: "2020-10-13"
tags: linux
---

# 安装

[vscode](https://code.visualstudio.com/),下载安装，安装后默认是英文界面，要改成中文的话可以点击左侧的扩展按钮搜索 “Chinese”，安装搜索结果中的第一个（Chinese (Simplified) Language Pack for Visual Studio Code）后重启软件即可变为中文。

# 安装远程开发扩展

点击扩展栏，然后搜索“Remote Development”，点击安装Remote Development即可。

之后安装git bash,别用windowns的openssh,[git](https://git-scm.com/download/)官网。

打开git bash,本地生成密匙：【生成4086长度密匙】

```shell
ssh-keygen -b 4096 -t rsa
```
本地操作将密匙复制到远程服务器：
```shell
ssh-copy-id -p 22 user@ip
```
或者
```shell
cat ~/.ssh/id_rsa.pub | ssh -p 22 username@remote-server "cat >> ~/.ssh/authorized_keys"
```
或者，也可以选择手动复制。
```shell
#新建~/.shh文件夹下authorized_keys
touch ~/.ssh/authorized_keys
#将~/.ssh/id_rsa.pub里的东西弄到新建的文件中保存即可。
```

之后，配置本地的~/.ssh/config文件。

```shell
Host linux
	HostName 公网ip
	User 用户名
	IdentityFile ~/.ssh/id_rsa
```

就可以无密码远程连接了。

# vscode使用R

教程[https://github.com/Ikuyadeu/vscode-R/wiki](https://github.com/Ikuyadeu/vscode-R/wiki)

1. 在vscode扩展中安装[vscode-R](https://marketplace.visualstudio.com/items?itemName=Ikuyadeu.r)和[https://marketplace.visualstudio.com/items?itemName=REditorSupport.r-lsp](https://marketplace.visualstudio.com/items?itemName=REditorSupport.r-lsp),均选择安装在远程主机上。

2. 远程主机的R上安装R包

```R
install.packages("languageserver")
```

3. 还有一个控制台也可以装下，比传统的R控制台方便。具体看[radian](https://github.com/randy3k/radian)，之后在vscode的设置中，选择刚刚安装的R插件的设置区域，增加radian的路经，具体看[https://github.com/Ikuyadeu/vscode-R/wiki/Installation:-Linux](https://github.com/Ikuyadeu/vscode-R/wiki/Installation:-Linux)

4. vscode设置快捷键，具体可看[https://github.com/Ikuyadeu/vscode-R/wiki/Keyboard-shortcuts](https://github.com/Ikuyadeu/vscode-R/wiki/Keyboard-shortcuts)
5. 设置r.sessionWatcher为true

# vscode更改主题
在文件->首选项有颜色主题和文件图标可以修改，icon我使用vscode-icons,主题我使用one dark pro

# vscode shell
- shell-format：格式化
- shellman：语法提示
- 安装Code Runner插件，可以实时运行脚本
- shellcheck: 语法错误检查
- Bash IDE:自动补全

# vscode自动添加header等

