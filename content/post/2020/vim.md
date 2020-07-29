---
title: vim
date: "2020-03-28"
tags: "linux"
summary: " "
---
vim 配置

bundle安装在~/.vim/bundle

如果要公用可以装在/opt/vimbundle/bundle/文件夹下，或者配置环境变量。

### 依赖包安装

确保环境中有`cmake` , `python-dev` , `python3-dev` ,没有的话需要事先安装

```shell
sudo apt-get install cmake python-dev python3-dev clang
```

### `YouCompleteMe` 及其依赖包下载

```shell
#在~/.vim/bundle/文件夹下
git clone https://github.com/ycm-core/YouCompleteMe.git
cd YouCompleteMe
# 下载依赖库，这一步与网速有关，可能会断掉报错，重新运行可以了。
git submodule update --init --recursive
```

### 编译`YouCompleteMe`

下载好`YouCompleteMe` 及其依赖包后，打开`vi` 发现仍然会报错，是因为其核心包`ycmd` 还没有被编译好，需要我们手动编译。

```shell
cd /opt/vimbundle/bundle/YouCompleteMe/
# 具体是支持哪个python版本看自己情况，我这边指支持python，所以用python编译。要不要支持c语言也看自己需要，不需要的话就去掉`--clang-completer`。
python install.py --clang-completer
```

### 可能的报错

如果编译好`ycmd_core` 后仍然报错，那很可能就是一些`python module` 没有安装，通过下面方法检测出来再安装即可。

```shell
cd /opt/vimbundle/bundle/YouCompleteMe/third_party/ycmd/
cp ycmd/default_settings.json .
# 通过下面查看是不是出现python的某些模块没有安装，如果是，那就安装上就好了。
python ycmd --options_file default_settings.json
```

一个示例配置

```shell
  
syntax on
set guifont=Monaco:h13
"set tabstop=4
set hlsearch
set nocompatible
set nofoldenable
set history=1000
set backspace=indent,eol,start
" prompt when existing from an unsaved file
set confirm
set t_Co=256
set title
set autoread
set spell
set cursorline
set cursorcolumn
set fenc=utf-8
set encoding=utf-8
set langmenu=zh_CN.UTF-8

" bundle conf
filetype off
set rtp+=/mnt/d/UbuntuApps/vimbundle/bundle/Vundle.vim
call vundle#begin("/mnt/d/UbuntuApps/vimbundle/bundle/")
Plugin 'VundleVim/Vundle.vim'
Plugin 'vim-pandoc/vim-rmarkdown'
Plugin 'vim-pandoc/vim-pandoc-syntax'
Plugin 'vim-pandoc/vim-pandoc'
Plugin 'iamcco/markdown-preview.vim'
Plugin 'iamcco/mathjax-support-for-mkdp'
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdown'
Plugin 'elzr/vim-json'
Plugin 'tomasr/molokai'
Plugin 'flazz/vim-colorschemes'
Plugin 'Valloric/YouCompleteMe'
Plugin 'jalvesaq/Nvim-R'
Plugin 'scrooloose/nerdtree'
Plugin 'vim-airline/vim-airline'
Plugin 'majutsushi/tagbar'
Plugin 'jalvesaq/vimcmdline'
call vundle#end()
filetype plugin indent on

" YouComleteMe conf
let g:ycm_use_clangd = 0
let g:ycm_server_python_interpreter='/usr/bin/python3'
let g:ycm_global_ycm_extra_conf='/mnt/d/UbuntuApps/vimbundle/bundle/.ycm_extra_conf.py'
let g:ycm_confirm_extra_conf = 0
let g:ycm_seed_identifiers_with_syntax = 1
let g:ycm_complete_in_comments = 1
let g:ycm_collect_identifiers_from_comments_and_strings = 0
let g:ycm_complete_in_strings = 1
let g:ycm_cache_omnifunc = 1
let g:ycm_autoclose_preview_window_after_completation=1
let g:ycm_min_num_of_chars_for_completion=3
autocmd InsertLeave * if pumvisible() == 0 | pclose | endif

" Nvim-R conf
let R_assign = 0
let R_objbr_place = 'script,right'

" vim-colorschemes conf
colorscheme wombat

" vim-json conf
let g:vim_json_syntax_conceal = 0

" nerdtree conf
nmap <F2> :NERDTreeToggle<CR>
let NERDChristmasTree=0
let NERDTreeChDirMode = 2
let NERDTreeShowBookmarks = 1
let NERDTreeIgnore = ['\~$', '\.pyc$', '\.swp$']
let NERDTreeWinSize = 25
let NERDTreeWinPos = "left"

" vim-airline conf
let g:airline_powerline_fonts=0
let g:airline#extensions#tabline#enabled=1
let g:airline#extensions#tabline#buffer_nr_show=1
let g:airline#extensions#tagbar#enabled=0

" tagbar conf
nmap <F8> :TagbarToggle<CR>
let g:tagbar_left=0
let g:tagbar_width=30
let g:tagbar_autofocus = 1
let g:tagbar_sort = 0
let g:tagbar_compact = 1
" tag for coffee
if executable("coffeetags")
    let g:tagbar_type_coffee = {
          \ 'ctagsbin' : 'coffeetags',
          \ 'ctagsargs' : '',
          \ 'kinds' : [
          \ 'f:function',
          \ 'o:object',
          \ ],
          \ 'sro': ".",
          \ 'kind2scope' : {
          \ 'f' : 'object',
          \ 'o' : 'object',
          \ }
          \ }
    let g:tagbar_type_markdown = {
          \ 'ctagstype' : 'markdown',
          \ 'sort' : 0,
          \ 'kinds': [
          \ 'h:sections'
          \ ]
          \ }
endif

" markdown-preview
let g:mkdp_path_to_chrome = "/usr/bin/firefox"
```

> 别人的，做个记录。