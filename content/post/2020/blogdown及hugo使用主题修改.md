---
title: blogdown及hugo使用主题修改
date: "2020-08-16"
tags: "R"
toc: true
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

## 引入行内公式

我通常会用符号\$作为一个行内数学公式的标识符。但是貌似现在除了Pandoc会解析外，其他都没有这个支持。

[https://yihui.org/cn/2017/04/mathjax-markdown/](https://yihui.org/cn/2017/04/mathjax-markdown/)提到了怎么修改。

[https://gohugo.io/getting-started/configuration-markup/#goldmark](https://gohugo.io/getting-started/configuration-markup/#goldmark)hugo配置文件教程。

目前默认使用goldmark渲染markdown。（注：没弄好。）

## 修改table目录

正如上面goldmark配置链接，默认

```html
 [markup.tableOfContents]
    endLevel = 3
    ordered = false
    startLevel = 2
```

开始解析是从标题head2 开始，改成1开始。

## 浮动目录

[link链接修改](https://css-tricks.com/css-basics-styling-links-like-boss/)

[hugo shortcode](https://fakedev.com/2020/hugo-custom-shortcodes-template.html)

[hugo toc修改](https://linuxer.io/posts/hugo-toc/)

对于普通的markdown文件，可以使用{{.TableOfContents}}进行设置，对于这个主题，你可以修改主题文件中的toc.html

```html
{{ if (.Params.toc) }}
<aside>
  <div id='anchors-navbar'>
  <i class='fas fa-anchor'></i>
  {{.TableOfContents}}
  </div>
</aside>
{{ end }}
```

之后根据class id属性修改css。

```CSS
/* formatting table of contents */
#TableOfContents {
    background: #333399;
    display: table;
    font-size: 75%;
    margin-bottom: 1em;
    padding: 0px;
    width: auto;
}
#anchors-navbar {
    /*border: 1px solid;*/
    /*border-radius: 1px;*/
    -webkit-box-shadow: 10px 20px 30px 10px teal;
    /*box-shadow: 0 6px 12px #333333;*/
    background-clip: padding-box;
    padding: 6px 6px;
    position: fixed;
    right: 50px;
    top: 68px;
    font-size: 14px;
    /*white-space: nowrap;*/
    z-index: 999;
    cursor: pointer;
    text-align: left;
    max-height: 50%;
    overflow-y: auto;
    overflow-x: hidden;
    /*display: table;*/
    width: auto;
    max-width: 300px;
    background: #333399;
}

#anchors-navbar ul{
    display: none;
    text-align: left;
    padding-right: 10px;
    padding-left: 18px;
    padding-top: 5px;
    list-style-type: lower-greek;
    margin-left: 10px;
}

#anchors-navbar:hover ul{
    display: block;
}

#anchors-navbar ul li a {
    text-decoration: none;
    border-bottom: none;
    font-size: 18px;
    color: #ff69b4;
    background: 0 0;
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
    position: relative;
    font-weight: bold;
}

#anchors-navbar ul li a:hover {
    text-decoration: underline;
    font-weight: bold;
    color: #CCCCCC;
}

#anchors-navbar ul li .title-icon {
    padding-right: 4px;
}
.fa-anchor {
    color:#FF6600;
    /*background-color: #333333;*/
    text-shadow: 1px 1px 1px #ccc;
    font-size: 2em;
}
```

**加入js**

```javascript
iterativeUL: function($dom) {
    var li_list = []
    $dom.children("li").each(function(i, item) {
        var _li = { 
            url: $(item).children("a").attr("href"), 
            name: $(item).children("a").text(),
            children: []
        }   
        $sub_ul = $(item).children("ul")
        if ($sub_ul.length > 0) {
            _li.children = main.iterativeUL($sub_ul)
        }   
        li_list.push(_li)
    })  
    return li_list
};
iterativeUI: function(root, template, prefix) {
    template += "<ul>"
    $.each(root, function(i, item) {
        var next_prefix = prefix + String(i+1) + "." 
        template += '<li>'+
                        '<i class="fa fa-hand-o-right" aria-hidden="true"></i>'+
                        '<span class="title-icon "></span>'+
                        '<a href="99991997"><b>99991998  </b>99991999</a>'
                           .replace("99991997", item.url)
                           .replace("99991999", item.name)
                           .replace("99991998", next_prefix) +
                    '</li>'
        if (item.children.length > 0) {
            template = main.iterativeUI(item.children, template, next_prefix)
        }   
    })  
    template += "</ul>"
    return template
};
initNavigations: function() {
    var $navigations = $("#TableOfContents");
    /* 这是个大坑, 需要大于号>来限制只选择一级子元素，否则会有多组ul被匹配到 */
    var root = main.iterativeUL($("#TableOfContents > ul"))
    if (root.length <= 0) {
        return;
    }

    var html = main.iterativeUI(root, '', '')

    //重新替换Toc模板
    $navigations.html(html)

    //由于导航栏固定,所以调整目录锚点往上偏移导航栏高度的距离
    var fixSet = $("#main-navbar").height() + 10; 
    $('nav#TableOfContents a[href^="#"][href!="#"]').click(function(e) {
        e.preventDefault();
        $('html, body').animate({scrollTop: $(decodeURI(this.hash)).offset().top - fixSet}, 400);
    }); 
};
```

至于Rmarkdown,你可以修改后缀名.Rmd为.Rmarkdown,这两个区别见[https://bookdown.org/yihui/blogdown/output-format.html](https://bookdown.org/yihui/blogdown/output-format.html)

或者knitr编译成.md文件，然后由hugo解析。

[http://estebanmoro.org/post/2019-02-04-setting-up-your-blog-with-rstudio-and-blogdown-iii-modify-your-theme/](http://estebanmoro.org/post/2019-02-04-setting-up-your-blog-with-rstudio-and-blogdown-iii-modify-your-theme/)

[https://dadascience.design/post/r-some-tricks-when-working-with-blogdown-hugo-working-draft/](https://dadascience.design/post/r-some-tricks-when-working-with-blogdown-hugo-working-draft/)

当然，最好的方法是直接修改blogdown的pandoc模板，blogdown运行`blogdown::serve_site`等函数使用bloddown::html_page直接生成.html文件的，中间是使用pandoc进行markdown解析的。

```R
blogdown:::pkg_file('resources', 'template-minimal.html')
```

可以找到这个简单的模板文件。

原来的：

```R
$if(toc)$
<div id="$idprefix$TOC">
$toc$
$endif$
```

可以加上刚刚使用toc float的类

```R
$if(toc)$
<aside>
<div id='anchors-navbar'>
<i class='fas fa-anchor'></i>
<div id="$idprefix$TOC">
$toc$
</div>
</div>
</aside>
$endif$
```

这样就解决了。

但是这样可不是长久之计，毕竟一更新这个包不就没了。

还有高亮跟加入toc在yaml中使用

```R
output:
  blogdown::html_page:
    highlight: zenburn
    toc: true
```

发现只有这个高亮适合这主题。

## **添加分页**

[https://bookdown.org/yihui/blogdown/templates.html#how-to](https://bookdown.org/yihui/blogdown/templates.html#how-to)

[https://lvv.me/posts/2019/12/26_hugo_theme_dev_tips/](https://lvv.me/posts/2019/12/26_hugo_theme_dev_tips/)

最后我的list文件是这样的

```html
<!DOCTYPE html>
<html lang="{{ .Site.LanguageCode }}">

{{ partial "header.html" . }}

<body>
{{ partial "default_header_image.html" . }}

<div class="main-content 
site-main section-inner thin animated fadeIn faster">

{{ partial "menu_strip.html" . }}

<h1>{{ .Title | markdownify }}</h1>

{{ .Content }}

<div class="prettylistcontainer">
<ul class="prettylist">
  <!-- range (where .Data.Pages "Section" "!=" "") -->
   {{ $paginator := .Paginate .Data.Pages }}
   {{ range $paginator.Pages }}
<li class="prettylist">
<p class="prettylist">
<a href="{{ .URL }}">{{ .Title | markdownify }}</a>&nbsp;&nbsp;
<span class="prettylistdate">{{ .Date.Format "02 Jan 2006" }}</span>
<div class="posts-group">

      
            
            
<br>
<!-- 
<span class="prettylistsummary">{{ .Summary | markdownify }}</span>
-->
</p>
</li>
{{ end }}
</ul>
</div>
{{ partial "pagination.html" . }} 
{{ partial "footer.html" . }}

</div>
</body>
</html>
```

\partial目录下的pagination.html文件如下

```html
 {{ if or (.Paginator.HasPrev) (.Paginator.HasNext) }}
 <div class="pagination">
     {{- if .Paginator.HasPrev }}
     <a class="pagination__item pagination__item--prev btn" href="{{ .Paginator.Prev.URL }}">«</a>
     {{- end }}
     <span class="pagination__item pagination__item--current">{{ .Paginator.PageNumber }}/{{ .Paginator.TotalPages }}</span>
     {{- if .Paginator.HasNext }}
     <a class="pagination__item pagination__item--next btn" href="{{ .Paginator.Next.URL }}">»</a>
     {{- end }}
 </div>
 {{ end }}  
```

之后就是加css了，

```css
/*增加文章分页css*/
.pagination {
   margin-top: 20px
}

.pagination__item {
  display: inline-block;
  padding: 10px 15px;
   font-weight: 700;
   color: #333333;
   background: #336633
}

.pagination__item:hover, .pagination__item--current {
  color: #fff;
  background: #535e75
}
```

每页显示多少文章数，在站点配置文件改,默认10。

```R
Paginate = 10
```

具体看文章:[https://fffou.com/post/2020-05-14/](https://fffou.com/post/2020-05-14/)

## 添加RSS

hugo默认有RSS，你仔细看你的网站会发现有一个index.xml文件，但是谷歌插件不会自动识别这个文件，必须打开，不大友好。

在header.html添加，

```
<link rel="alternate" type="application/rss+xml" href="https://liripo.github.io/index.xml" title="Liripo">
```

这样会在你整个网站添加链接，不想的话可以只添加到你的主页。

## 添加站内搜素

[https://palant.info/2020/06/04/the-easier-way-to-use-lunr-search-with-hugo/](https://palant.info/2020/06/04/the-easier-way-to-use-lunr-search-with-hugo/)

我使用如下教程:

[https://gist.github.com/cmod/5410eae147e4318164258742dd053993](https://gist.github.com/cmod/5410eae147e4318164258742dd053993)

除了文件需要修改下，其余均如文档所述，运行`CMD+/`即可对站内进行检索。

里面的fastsearch,js更改如下

```javascript
var fuse; // holds our search engine
var searchVisible = false; 
var firstRun = true; // allow us to delay loading json data unless search activated
var list = document.getElementById('searchResults'); // targets the <ul>
var first = list.firstChild; // first child of search list
var last = list.lastChild; // last child of search list
var maininput = document.getElementById('searchInput'); // input box for search
var resultsAvailable = false; // Did we get any search results?

// ==========================================
// The main keyboard event listener running the show
//
document.addEventListener('keydown', function(event) {

  // CMD-/ to show / hide Search
  if (event.metaKey && event.which === 191) {
      // Load json search index if first time invoking search
      // Means we don't load json unless searches are going to happen; keep user payload small unless needed
      if(firstRun) {
        loadSearch(); // loads our json data and builds fuse.js search index
        firstRun = false; // let's never do this again
      }

      // Toggle visibility of search box
      if (!searchVisible) {
        document.getElementById("fastSearch").style.visibility = "visible"; // show search box
        document.getElementById("searchInput").focus(); // put focus in input box so you can just start typing
        searchVisible = true; // search visible
      }
      else {
        document.getElementById("fastSearch").style.visibility = "hidden"; // hide search box
        document.activeElement.blur(); // remove focus from search box 
        searchVisible = false; // search not visible
      }
  }

  // Allow ESC (27) to close search box
  if (event.keyCode == 27) {
    if (searchVisible) {
      document.getElementById("fastSearch").style.visibility = "hidden";
      document.activeElement.blur();
      searchVisible = false;
    }
  }

  // DOWN (40) arrow
  if (event.keyCode == 40) {
    if (searchVisible && resultsAvailable) {
      console.log("down");
      event.preventDefault(); // stop window from scrolling
      if ( document.activeElement == maininput) { first.focus(); } // if the currently focused element is the main input --> focus the first <li>
      else if ( document.activeElement == last ) { last.focus(); } // if we're at the bottom, stay there
      else { document.activeElement.parentElement.nextSibling.firstElementChild.focus(); } // otherwise select the next search result
    }
  }

  // UP (38) arrow
  if (event.keyCode == 38) {
    if (searchVisible && resultsAvailable) {
      event.preventDefault(); // stop window from scrolling
      if ( document.activeElement == maininput) { maininput.focus(); } // If we're in the input box, do nothing
      else if ( document.activeElement == first) { maininput.focus(); } // If we're at the first item, go to input box
      else { document.activeElement.parentElement.previousSibling.firstElementChild.focus(); } // Otherwise, select the search result above the current active one
    }
  }
});


// ==========================================
// execute search as each character is typed
//
document.getElementById("searchInput").onkeyup = function(e) { 
  executeSearch(this.value);
}


// ==========================================
// fetch some json without jquery
//
function fetchJSONFile(path, callback) {
  var httpRequest = new XMLHttpRequest();
  httpRequest.onreadystatechange = function() {
    if (httpRequest.readyState === 4) {
      if (httpRequest.status === 200) {
        var data = JSON.parse(httpRequest.responseText);
          if (callback) callback(data);
      }
    }
  };
  httpRequest.open('GET', path);
  httpRequest.send(); 
}


// ==========================================
// load our search index, only executed once
// on first call of search box (CMD-/)
//
function loadSearch() { 
  fetchJSONFile('/index.json', function(data){

    var options = { // fuse.js options; check fuse.js website for details
      shouldSort: true,
      location: 0,
      distance: 100,
      threshold: 0.4,
      minMatchCharLength: 2,
      keys: [
        'title',
        'permalink',
        'tags',
        'content'
        ]
    };
    fuse = new Fuse(data, options); // build the index from the json file
  });
}


// ==========================================
// using the index we loaded on CMD-/, run 
// a search query (for "term") every time a letter is typed
// in the search box
//
function executeSearch(term) {
  let results = fuse.search(term); // the actual query being run using fuse.js
  let searchitems = ''; // our results bucket
  if (results.length === 0) { // no results based on what was typed into the input box
    resultsAvailable = false;
    searchitems = '';
  } else { // build our html 
    for (let item in results.slice(0,5)) { // only show first 5 results
       searchitems = searchitems + '<li><a href="' + results[item].item.permalink + '" tabindex="0">' + '<span class="title">' + results[item].item.title + '</span><br /> <span class="sc">' + results[item].item.tags + '</em></a></li>';
    }
    resultsAvailable = true;
  }

  document.getElementById("searchResults").innerHTML = searchitems;
  if (results.length > 0) {
    first = list.firstChild.firstElementChild; // first result container — used for checking against keyboard up/down location
    last = list.lastChild.firstElementChild; // last result container — used for checking against keyboard up/down location
  }
}
```

serach.css如下

```css
#fastSearch { 
  visibility: hidden;
  position: fixed;
  right: 0px;
  top: 0px;
  display: inline-block;
  width: 300px;
}      

#fastSearch input { 
  padding: 4px 10px;
  width: 100%;
  height: 31px;
  font-size: 1.6em;
  color: #aaa;
  font-weight: bold;
  background-color: #000;
  border-radius: 3px 3px 0px 0px;
  border: none;
  outline: none;
  text-align: left;
  display: inline-block;
}

#searchResults li { 
  list-style: none; 
  margin-left: 0em;
  background-color: #333; 
  border-bottom: 1px dotted #000;
}
  #searchResults li .title { font-size: 1.1em; margin-bottom: 10px; display: inline-block;}

#searchResults { visibility: inherit; display: inline-block; width: 320px; }
#searchResults a { text-decoration: none !important; padding: 10px; display: inline-block; }
  #searchResults a:hover, a:focus { outline: 0; background-color: #666; color: #fff; }
```

index.json如下

```json
{{- $.Scratch.Add "index" slice -}}
{{- range .Site.Pages -}}{{- $.Scratch.Add "index" (dict "title" .Title "tags" .Params.tags "content" .Plain "permalink" .Permalink "date" .Params.date) -}}{{- end -}}
{{- $.Scratch.Get "index" | jsonify -}}
```