---
title: 站内搜索实现
tags:
  - hexo
  - 搜索
  - swiftype
categories:
  - 分享
date: 2016-04-28 17:11:36
---
<Excerpt in index | 首页摘要> 
实现静态网站站内搜索
参考了这篇文章 [hexo干货系列：（五）hexo添加站内搜索](http://www.jianshu.com/p/79161002694c)
因为大众的主题都有可能不同，根据自己的样式主题进行修改添加
本文以yelee主题为例
有兴趣的可以移步到 [hexo-theme-yelee](https://github.com/MOxFIVE/hexo-theme-yelee)


<!-- more -->
<The rest of contents | 余下全文>

步骤都是大同小异
主要是修改主题代码不同

> # **hexo的主题配置** #

yelee的搜索代码在左侧栏
直接到左侧栏的代码中修改

## 修改主题配置文件 ##
在修改前确保在主题下的``_config.yml``文件的末尾添加：
```bash
swift_search:
enable:  true
```
且原文中的``search_box``要设置为``false``

## 编辑搜索代码的文件 ##

```bash
vim youpath/themes/yelee/layout/_partial/left-col.ejs
```

在文件中查找``search_box`` ，一般在14行左，新增文章中的代码，即：
```javascript
 <% if (theme.swift_search.enable){ %>
     <form class="search" action="<%- config.root %>search/index.html" method="get" accept-charset="utf-8">
    ┊   <input type="text" id="search" class="st-default-search-input" maxlength="20" placeholder="Search" />
     </form>
  <%}%>  
```
在底部添加``JavaScript``代码( 这里的JavaScript代码是你swiftype的代码，每个人都不一样 )
```javascript
<script type="text/javascript">
    ┊(function(w,d,t,u,n,s,e){w['SwiftypeObject']=n;w[n]=w[n]||function(){
    ┊  (w[n].q=w[n].q||[]).push(arguments);
        };s=d.createElement(t);
    ┊  e=d.getElementsByTagName(t)[0];s.async=1;s.src=u;e.parentNode.insertBefore(s,e);
    ┊   ┊})(window,document,'script','//s.swiftypecdn.com/install/v2/st.js','_st');
     _st('install','5j5ZpzPaD88GzFExX5xW','2.0.0');
</script>
```
## 重新生成 ##
```bash
hexo clean
hexo g
```
