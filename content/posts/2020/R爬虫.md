---
title: R爬虫
date: "2020-03-28"
tags: "R"
summary: "R selenium"
---

# 静态网页
简单的静态网页使用rvest包完全可以胜任。

# 动态网页
需要移动鼠标点击鼠标的则可以使用RSelenium
其作用是用R调用Selenium Server，通过Selenium Server我们可以对网页进行操作，然后爬取操作后的数据，从而进行爬取动态页面。

## 初次使用
- java安装
- [谷歌驱动](http://npm.taobao.org/mirrors/chromedriver)安装,由于需要对应谷歌浏览器的版本，所以可以都使用最新的版本。下载,注意chrome驱动一定要和chrome浏览器对应 ,比如我的浏览器83.4109.... ，查看浏览器版本，点击谷歌浏览器右上【…】—【帮助】–【关于Google Chrome】就可以看到浏览器版本了。然后我就下载83开头的就行。
- 记得将下载的谷歌驱动与chrome.exe放在一个文件夹下，然后将这个文件夹路径添加至环境变量。如何添加环境变量可自行goole。
- [Selenium Standalone Server](https://www.selenium.dev/downloads/)下载,我下载的是selenium-server-standalone-3.141.59.jar
selenium server使用可以在文件所在目录运行
```shell
java -jar .\selenium-server-standalone-3.141.59.jar
```
或者直接在Rstudio中运行
```R
system('java -jar "C:/RSelenium/selenium-server-standalone-3.11.0.jar"',wait = FALSE)
```
**运行结束后勿关闭，最小化即可。**

# RSelenium使用与实例
爬取[富集分析网站](https://biit.cs.ut.ee/gprofiler/gost)示例代码

其中txt前几行如下
```R                                   
CESC  1 Cluster0 https://biit.cs.ut.ee/gplink/l/1WPg5kkuQq
CESC  2 Cluster1 https://biit.cs.ut.ee/gplink/l/cN9yKkqzSj
CESC  3 Cluster2 https://biit.cs.ut.ee/gplink/l/WEBiuG-4S_
```
>代码,里面添加了一些无限循环操作。
```R
library(rvest)
library(RSelenium)
library(fs)
library(tidyverse)
#连接浏览器
#运行server
isempty <- function(){
    html <- remDr$getPageSource()[[1]]
    html <- read_html(html)
    xpath <- "/html/body/div/div/div/div[3]/div[3]/div/div/div"
    htmltext <- html %>% html_nodes(xpath = "/html/body/div/div/div/div[3]/div[3]/div/div/div") %>% 
        html_text(trim = T)
    #页面没刷新情况
    if(rlang::is_empty(htmltext))return(TRUE) 
    #没有结果情况
    if(htmltext == "No results."){
        return("No results.")
    }else{
    boolen <- html %>% 
        html_nodes(".active+ .tab-button a") %>% 
        rlang::is_empty()
    }
}
isempty2 <- function(){
    html <- remDr$getPageSource()[[1]]
    html <- read_html(html)
    boolen <- html %>% 
        html_nodes(xpath = "/html/body/div/div/div/div[3]/div[3]/div/div/div[1]/div[2]/div[2]/div[1]/div/button[1]") %>% 
        rlang::is_empty()
}
#R中运行selenium server
system('java -jar "../selenium-server-standalone-3.141.59.jar"',
       wait = FALSE)
#------打开浏览器，模拟点击
remDr <- remoteDriver(
    browserName = "chrome",
    remoteServerAddr = "localhost",
    port = 4444L)
remDr$open()
txt <- read_csv("clusterurl.csv",col_names = F)
txt <- txt %>% separate(X2,sep = " ",into = c("row","cluster","url2"))
getgprofiler <- function(url){
    tmp <- txt %>% filter(url2 == url)
    remDr$navigate(url)
    #沉睡40秒
    Sys.sleep(30)
    #判断是否加载完全，不完全则一直沉睡,没有限定次数错误退出的情况。
    boolen1 <- isempty()
    #还有一种没有显著结果的
    if(boolen1 == "No results.")return("No results.")
    while(boolen1){
        remDr$navigate(url)
        Sys.sleep(20)
        boolen1 <- isempty()
    }
    x <- remDr$findElement(using = "css selector",value = ".active+ .tab-button a")
    remDr$mouseMoveToLocation(webElement=x)
    remDr$click()
    Sys.sleep(5)
    boolen2 <- isempty2()
    while(boolen2){
        remDr$mouseMoveToLocation(webElement=x)
        remDr$click()
        boolen2 <- isempty2()
    }
    xpath <- "/html/body/div/div/div/div[3]/div[3]/div/div/div[1]/div[2]/div[2]/div[1]/div/button[1]"
    l <- remDr$findElement(using = 'xpath',value = xpath)
    remDr$mouseMoveToLocation(webElement=l)
    #下载
    remDr$click()
    #下载这儿强制5秒
    Sys.sleep(5)
    #下载文件路径
    file <- fs::dir_ls("C:\\Users\\Liripo\\Downloads",
                   type = "file",regexp = "gProfiler_hsapiens.+csv$")
    #文件重命名
    while(rlang::is_empty(file)){
        #如果为空则重新点击下载
        remDr$mouseMoveToLocation(webElement=l)
        remDr$click()
        file <- fs::dir_ls("C:\\Users\\Liripo\\Downloads",
                           type = "file",regexp = "gProfiler_hsapiens.+csv$")
    }
    #重命名文件并移动
    filename <- paste(tmp[[1]],tmp[[3]],"gprofiler",sep = "-")
    file_move(path = file,
            new_path = paste0("G:\\liripo\\Rwork\\20200622-clusterresult\\",
                            filename,".csv"))
}
lapply(txt$url2[1:5],getgprofiler)
```
