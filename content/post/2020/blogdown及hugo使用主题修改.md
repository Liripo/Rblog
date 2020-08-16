---
title: blogdown及hugo使用主题修改
date: "2020-08-16"
tags: "R"
---

# blogdown

[https://github.com/rstudio/blogdown](https://github.com/rstudio/blogdown)在bookdown上有书，所以安装使用基本看下文档就知道了。

当然，你也可以选择hugodown。

# calade theme

我使用的是[https://github.com/djnavarro/hugo-calade](https://github.com/djnavarro/hugo-calade)这个主题。

hugo以及这个主题，一些默认的设置，比如数学公式，使用rstuio服务，中国比较难以加载。

## mathjax改成cdn服务

在网站根目录下，\layouts\partials\foot_custom.html配置如下

```html
<!--
<script src="//yihui.name/js/math-code.js"></script>
<script async src="//mathjax.rstudio.com/latest/MathJax.js?config=TeX-MML-AM_CHTML"></script>
-->
<script async src="{{ "/js/center-image.js" | relURL }}"></script>
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
<center>
<!--
用户名自己修改，加入网站运行时间。
-->
Liripo <SPAN id=span_dt_dt style="color: #0196e3;"></SPAN>
<SCRIPT language=javascript>
function show_date_time(){
window.setTimeout("show_date_time()", 1000);
BirthDay=new Date("7/8/2019 00:00:00");//日期自己修改
today=new Date();
timeold=(today.getTime()-BirthDay.getTime());
sectimeold=timeold/1000
secondsold=Math.floor(sectimeold);
msPerDay=24*60*60*1000
e_daysold=timeold/msPerDay
daysold=Math.floor(e_daysold);
e_hrsold=(e_daysold-daysold)*24;
hrsold=Math.floor(e_hrsold);
e_minsold=(e_hrsold-hrsold)*60;
minsold=Math.floor((e_hrsold-hrsold)*60);
seconds=Math.floor((e_minsold-minsold)*60);
span_dt_dt.innerHTML=""+daysold+"天"+hrsold+"小时"+minsold+"分"+seconds+"秒";
}
show_date_time();
</SCRIPT>
</center>
```

## 添加不蒜子统计url访问数

[不蒜子](http://busuanzi.ibruce.info/)是 Bruce 开发的一款轻量级的网页计数器,轻量。

可以看下文章:[https://fuckcloudnative.io/posts/hugo-add-busuanzi/](https://fuckcloudnative.io/posts/hugo-add-busuanzi/)有说hugo怎么添加不蒜子，并修改初始访问人数，不过我没用。

我在hugo-calade\layouts\partials\footer.html改成如下配置：

```html
<footer>
  <!-- highlight.js -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.1.2/highlight.min.js"></script>
  <!-- and it's easy to individually load additional languages -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.1.2/languages/r.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/10.1.2/languages/python.min.js"></script>

  <script>
  hljs.configure({languages: []});
  hljs.initHighlightingOnLoad();
  </script>
  <!--添加不蒜子-->
  <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
  <span id="busuanzi_container_site_pv">本站总访问量<span id       ="busuanzi_value_site_pv"></span>次</span>
  <span id="busuanzi_container_site_uv">
    本站访客数<span id="busuanzi_value_site_uv"></span>人次
</span>
  <!---->
  {{ partial "foot_custom.html" . }}
  {{ with .Site.Params.footer }}
  <br/><br/>
  {{ . | markdownify }}
  <br/><br/>
  {{ end }}
</footer>
```



在主题目录下，hugo-calade\layouts\_default中的single.html(这个文件用于修改文章的html)

```html
<!DOCTYPE html>
<html lang="{{ .Site.LanguageCode }}">

  {{ partial "header.html" . }}

  <body>
    {{ partial "header_image.html" . }}

    <div class="main-content">
      {{ partial "menu_strip.html" . }}
      {{ partial "post_title.html" . }}

      <main>
        {{ partial "toc.html" . }}
        {{ .Content }}
      </main>

      {{ partial "footer.html" . }}

    </div>
  </body>
</html>
```

可以看到这个文件在body中引入了partial文件夹下的post_title.html等文件，我们修改post_title.html这文件如下：

```html
<div>
  <h1>{{ .Title | markdownify }}</h1>
  <h3>
    {{ if (gt .Params.date 0) }}{{ .Date.Format "02 Jan 2006" }}{{ end }}
  </h3>
   <h5 id="wc">{{ .WordCount }} Words|Read in about {{ .ReadingTime }}       Min|本文总阅读量<span id="busuanzi_value_page_pv"></span>次</h5>
    </h5>
  <br><br>
</div>
```

这样就加入了文章的字数。不过hugo默认不统计中文，所以还需要在站点文件config.toml加入

```html
#添加中文计数
hasCJKLanguage = true 
```

还有个注意点，就是那些没有缩进的配置应该放在

```html
[permalinks]
```

等配置前面，才能识别。

## 嵌入B站视频

参考文章：[https://mogeko.me/2020/079/](https://mogeko.me/2020/079/)

更多的，可以看这篇文章:[https://www.xiangyunhuang.com.cn/2019/05/another-hello-markdown/](https://www.xiangyunhuang.com.cn/2019/05/another-hello-markdown/)

## 插入音乐

[http://tool.liumingye.cn/music/](http://tool.liumingye.cn/music/)这网站下歌不错。

## 修改表格样式

修改主题hugo-calade\static\css下的style.css文件

```css
/* table formatting
table {
  margin: auto;
  border-top: 1px solid #666;
  border-bottom: 1px solid #666;
}
table thead th { border-bottom: 1px solid #ddd; }
th, td { padding: 5px; }
thead, tfoot, tr:nth-child(even) { background: #eee; }
*/
table {
  border: 4px solid #000000;
  margin: auto;
  /*width: 100%;*/
  text-align: left;
  border-collapse: collapse;
}
table td, table th {
  border: 1px solid #000000;
  padding: 8px 6px;
}
table tbody td {
  font-size: 18px;
}
table thead {
  background: #CFCFCF;
  background: -moz-linear-gradient(top, #dbdbdb 0%, #d3d3d3 66%, #CFCFCF 100%);
  background: -webkit-linear-gradient(top, #dbdbdb 0%, #d3d3d3 66%, #CFCFCF 100%);
  background: linear-gradient(to bottom, #dbdbdb 0%, #d3d3d3 66%, #CFCFCF 100%);
  border-bottom: 4px solid #000000;
}
table thead th {
  font-size: 20px;
  font-weight: bold;
  color: #000000;
  text-align: left;
  font-family: "Arial Black";
}
table tfoot {
  font-size: 14px;
  font-weight: bold;
  color: #000000;
  border-top: 3px solid #000000;
}
table tfoot td {
  font-size: 14px;
}
```

## hugo markdown

hugo markdown是小于pandoc markdown的，所以想加入突出显示得改用html的mark标签。

为了hugo能够解析markdown文件中的html标签，可以在config.toml文件加入

```html
[markup.goldmark.renderer]
unsafe = true
```

这样就可以增加mark等突出显示的标签了。具体详见：[https://gohugo.io/news/0.60.0-relnotes/](https://gohugo.io/news/0.60.0-relnotes/)

## 引入谷歌字体

在主题的footer.html中加入

```html
<link rel="stylesheet" href="https://fonts.loli.net/css?family=Tangerine">
```

具体参考[https://sb.sb/blog/css-cdn/](https://sb.sb/blog/css-cdn/),将谷歌字体api，改为使用loli.net,[谷歌api](https://developers.google.com/fonts/docs/getting_started)。

之后引入这个字体作为mark的css。

```css
/*加入突出高亮*/
mark {
  font-family: Tangerine;
  font-weight: bold;
  background-color: #333333;
  text-shadow: 4px 4px 4px #aaa;
}
```

