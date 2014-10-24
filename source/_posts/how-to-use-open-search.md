title: 使用Open Search快速搜索 
date: 2014-10-24 08:32:34
tags:
---

##Open Search是什么
用户访问某个网站，如果该网站具备搜索功能，并提供遵循 OpenSearch 规范的代码，那么支持 OpenSearch 功能的浏览器就能够进行快捷搜索。 比如，Chrome 浏览器地址栏输入 github，再按 tab 键即可输入搜索内容。

##如何使用

##哪些网站支持
下面列出来的是我目前知道正在使用的，欢迎大家给我留言你知道支持`Open Search`的网站进行补充。

- github.com
- gfsoso.com
- stackoverflow.com
- baidu.com
- sogou.com
- tinysou.com
- taobao.com

##如何使你网站支持Open Search

### 1.假设一个网站支持搜索，且搜索连接为

	http://example.com/search.html?q=beijing	

### 2.添加Open Search的配置描述文件opensearch.xml，并放到你的网站可访问的地方，如网站跟目录下

``` xml
<OpenSearchDescription xmlns="http://a9.com/-/spec/opensearch/1.1/" xmlns:moz="http://www.mozilla.org/2006/browser/search/">
<ShortName>Example</ShortName>
<Description>Search Example</Description>
<InputEncoding>UTF-8</InputEncoding>
<Image width="16" height="16" type="image/x-icon">https://example.com/favicon.ico</Image>
<Url type="text/html" method="get" template="https://example.com/search?q={searchTerms}&ref=opensearch"/>
</OpenSearchDescription>
```

### 3.在你网站的主页添加对该描述文件的引用

在HTML的`head`标签中添加如下代码：

``` html
<link type="application/opensearchdescription+xml" href="/opensearch.xml" title="example">
```
