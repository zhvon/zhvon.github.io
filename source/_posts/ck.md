---
title: ckeditor + ckfinder 的集成
tags:
  - 工具插件
  - 富文本
  - 文件管理器
categories:
  - 工具插件
date: 2017-02-17 12:23:51
---
<Excerpt in index | 首页摘要> 
- 搭建独立的 ckeditor 
- 搭建独立的 ckfinder 
- ckeditor + ckfinder 集成

# 安装ckeditor #
## 下载``ckeditor`` ## 

[官网下载](http://ckeditor.com/download)
[也可以自己定制](http://ckeditor.com/builder)
[官方文档](http://docs.ckeditor.com/)

## 安装``ckeditor``到项目里 ##
- 引入js文件 ``<script src="../ckeditor.js"></script>``
- 替代textarea
```html
<html>
    <head>
        <meta charset="utf-8">
        <title>A Simple Page with CKEditor</title>
        <!-- Make sure the path to CKEditor is correct. -->
        <script src="../ckeditor.js"></script>
    </head>
    <body>
        <form>
            <textarea name="editor1" id="editor1" rows="10" cols="80">
                This is my textarea to be replaced with CKEditor.
            </textarea>
            <script>
                // Replace the <textarea id="editor1"> with a CKEditor
                // instance, using default configuration.
                CKEDITOR.replace( 'editor1'  );
            </script>
        </form>
    </body>
</html>
```
**ckeditor 安装完成**
<!-- more -->
<The rest of contents | 余下全文>
# 搭建单独的CKFinder(php版) #
> ckfinder最新版本要付费，即使安装了也是测试版本，无法上传和管理文件.
  在网上找到一个3.0的破解版本，暂时还没发现有什么bug，固用之。[传送门](http://download.csdn.net/download/mmkk12345678/9549293)

- 下载php版的``CKFinder``，将``ckfinder``i文件夹拷贝到任何``apache``可以访问到的虚拟目录对应的硬盘路径，是其包含的php文件可以被访问到。
- 创建一个资源文件夹用以存放我们上传的资源。
- 编辑``ckfinder``下的``config.php``配置文件
- 整合``CKFinder``和``CKEditor``（以js的形式）
```javascript
/*引入 ckfinder.js */
var editor = CKEDITOR.replace( 'main_html'  );
CKFinder.setupCKEditor( null, 'http://localhost/assets/cms/js/ckfinder/' );
```
** 如果``.setupCKEditor``的第一个参数为null则默认，将集成页面上的所有``CKEditor``  **
详细请看 [CKFinder（php版）的使用，以及与现有的CKEditor的集成](http://jingpin.jikexueyuan.com/article/46680.html)

至此，已完成，enjoy it
