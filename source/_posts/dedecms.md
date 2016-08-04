---
title: 编码
tags:
  - 编码
categories:
  - 分享
date: 2016-04-20 11:34:25
---
<Excerpt in index | 首页摘要> 
常用编码大概分为4种
``ASCII``编码：最早的编码，包含大小写英文字母、数字和一些符号。
``GB2312``编码：中文处理需要两个字节又不能和``ASCII``编码冲突，中国定制把中文编进去。
``Unicode``：把所有语言都统一到一套编码里，避免出现乱码。
``utf-8``：可变长编码，常用英文字母编码为1个字节，汉字为3个，很生僻字符才编码成4-6个字节。

<!-- more -->
<The rest of contents | 余下全文>

![](http://7xsuc5.com1.z0.glb.clouddn.com/image/utf8/1.png)
从上面的表格还可以发现，UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。

搞清楚了ASCII、Unicode和UTF-8的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：

在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。

用记事本编辑的时候，从文件读取的UTF-8字符被转换为Unicode字符到内存里，编辑完成后，保存的时候再把Unicode转换为UTF-8保存到文件：

![](http://7xsuc5.com1.z0.glb.clouddn.com/image/utf8/2.png)

浏览网页的时候，服务器会把动态生成的Unicode内容转换为UTF-8再传输到浏览器：


![](http://7xsuc5.com1.z0.glb.clouddn.com/image/utf8/3.png)
所以你看到很多网页的源码上会有类似<meta charset="UTF-8" />的信息，表示该网页正是用的UTF-8编码。
