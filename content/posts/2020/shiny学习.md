---
title: shiny学习
date: "2020-03-27"
tags: "R"
summary: " "
---
# shiny开发应用程序
shiny目前可以开发成移动端，web应用程序和打包成exe文件（很麻烦）。

# shiny比较重要的点及函数
shiny程序会自动加载R文件夹下的R脚本，css,js,png等可以放在www文件夹下，链接时必须省略www/,data数据放置data文件夹下

req()、validata()是没有合理输入暂停的利器，reactive反应性有必要学习，这两个的组合就是一个eventReactive()函数了吧。
isolate()函数，parse()函数，envl()
