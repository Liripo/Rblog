---
title: nvim
date: "2020-09-03"
tags: "linux"
---

# nvim安装

[https://neovim.io/](https://neovim.io/)

[https://github.com/neovim/neovim/releases](https://github.com/neovim/neovim/releases)  ====版本发布页面。

neovim的linux版本你可以在上面找到，你可以选择nvim-linux.tar.gz里面有编译好的nvim文件，也可以选择官网说的appimage安装，不过appimage怎么装我也不清楚。



## 配置


nvim的配置文件为`~/.config/nvim/init.vim`,没有则自己创键

插件管理选择[vim-plug](https://github.com/junegunn/vim-plug)

### 插件管理文件安装

```shell
mkdir -p ~/.var/app/io.neovim.nvim/data/nvim/site
curl -fLo ~/.var/app/io.neovim.nvim/data/nvim/site/autoload/plug.vim https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
#建个文件夹作为插件安装目录吧
mkdir ~/.var/app/io.neovim.nvim/data/nvim/site/plugged
```

完成上述步骤后在`init.vim`中加入代码

```shell
""""""""插件管理
let plug_install = 0
let autoload_plug_path = stdpath('config') . '/autoload/plug.vim'
if !filereadable(autoload_plug_path)
    silent exe '!curl -fL --create-dirs -o ' . autoload_plug_path .
        \ ' https://raw.github.com/junegunn/vim-plug/master/plug.vim'
    execute 'source ' . fnameescape(autoload_plug_path)
    let plug_install = 1
endif
unlet autoload_plug_path
```

这个代码使得nvim识别到vim-plug插件。当你使用vim时，你则无需`~/.vimrc`中加入。

## nvim中R的使用

首先自动补全及vim中开启R的终端需要的插件，`init.vim`

```shell
"配置安装目录及所需插件
call plug#begin('~/.var/app/io.neovim.nvim/data/nvim/site/plugged')
Plug 'jalvesaq/Nvim-R'
"Plug 'ycm-core/YouCompleteMe'
Plug 'gaalcaras/ncm-R'
Plug 'ncm2/ncm2'

" Optional: for snippet support
" Further configuration might be required, read below
Plug 'sirver/UltiSnips'
Plug 'ncm2/ncm2-ultisnips'
call plug#end()
filetype plugin indent on
""""""""""""""""""""""""""
```

使用`:PlugInstall`安装插件

加入提示你没有python3的话，你需要在`init.vim`添加

```shell
let g:python3_host_prog = "/mnt/d/linux/software/miniconda3/bin/python"
```

上述路径修改为你的python路径。

加入配色主题

```shell
"配色主题
Plug 'sheerun/vim-polyglot'
```

## 使用

打开vim后，在命令行模式输入`\rf`即可打开R会话，`\l`可以发送代码到R会话。

nvim重要键绑定：

- `Ctrl-x Ctrl-o`自动补全，

- `CTRL`-xf来完成字符串下文件名
- 