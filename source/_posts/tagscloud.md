---
title: 3D标签云
tags:
  - tagscanvas
  - 标签云
  - javascript
categories:
  - 分享
date: 2016-04-29 16:48:58
---
<Excerpt in index | 首页摘要> 
3D标签云，效果 [是这样的](http://blog.zhvon.com/tags/)
使用TagCanvas实现，[官网传送门](http://www.goat1000.com/tagcanvas.php#links)

---

使用有两种方式
1、单机模式
2、插件模式
详细了解可以移步到官网

---

因为当前环境已经支持了``jQuery``，这里使用插件模式
首先获取辅助文件，[Downloads](http://www.goat1000.com/tagcanvas.php#links)
本文使用插件模式，到 [这里](http://www.goat1000.com/jquery.tagcanvas.js?2.9)复制代码保存为文件

---

<!-- more -->
<The rest of contents | 余下全文>

添加一下代码到目标文件
主要引用的路径修改和id的对应

---

```html
<div class="tags" id="tags" style="display:none">
...
you tag list
...
</div>
```

```javascript
<script src="/js/jquery.tagcanvas.js"></script>

<div id="myCanvasContainer">
    <canvas width="300" height="300" id="my3DTags">
        <p>Anything in here will be replaced on browsers that support the canvas element!</p>
    </canvas>
</div>

<script>
    $(document).ready(function(){})
        $("#my3DTags").tagcanvas({textFont:"Georgia,Optima",textColour:null,outlineColour:"#ff00ff",weight:!0,reverse:!0,depth:.8,maxSpeed:.05,bgRadius:1,})
        freezeDecel:!0},"tags")||$("#myCanvasContainer").hide()});
</script>
```

finish~! enjoy it..
