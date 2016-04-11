---
title: hexo
date: 2016-04-08 17:03:26
tags: hexo，学习相关
---

折腾了下hexo，基本环境和部署好了，特意地马一下。

> 1. 环境搭建（nvm，npm，git）
> 2. 部署到github

***


># **环境的搭建** #

node.js通过nvm管理

- 使用git克隆nvm包到linux下

```bash
git clone https://github.com/creationix/nvm.git
```

- 执行以下命令即可吧nvm安装完毕。

```bash
cd nvm
bash install.sh
```
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p1.png)

- 根据需要，安装相应版本的node.js，这里我用最新的4.4.2
```bash
nvm install 4.4.2
```
好了，现在node.js安装好了，接下来进行hexo的安装。

根据官方的指引，执行 npm install -g hexo-cli

但很多人都会出现错误无法安装成功。出现以下错误：
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p2.png)

主要问题是官方的源被墙了，这里我们替换成淘宝的源
```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p3.png)

因为换成了淘宝的源所以命令行npm改成cnpm，即：
```bash
cnpm install -g hexo-cli
```
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p4.png)

查看一下版本
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p5.png)

hexo server 启动下，默认是4000端口
在浏览器中输入 you_host_name:4000（自行替换hostname）
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p6.jpg)

至此，hexo环境搭建完毕，enjoy it！

----------
