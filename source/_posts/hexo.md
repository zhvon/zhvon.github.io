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


># **部署到github** #

因为hexo生成的是静态页面，很适合托管到服务器上。
这里选择github。
国内比较常用的应该是coding.net，简单玩了和github差不多，有兴趣的可以尝试。
本文以github为例。

这里说个简单的思路

> 利用github的github pages
创建两个分支
一个分支是一份完整的代码（即整个更目录）
一个分支是发布的代码（即生成的静态文件）
这样发布和修改就很好地管理起来
大概就是deploy的分支设置为发布的分支，根目录设置为完整代码的分支

---

> **搭建步骤**
> 
>  1. 创建仓库，你的用户名.github.io（以下用 exmaple.github.io代替）
>  2. 创建两个分支：master(创建仓库的时候已经有了)和hexo
>  3. 使用git clone git@github.com:以下用exmaple/exmaple.github.io.git拷贝仓库，然后进行一系列的安装（npm
> install hexo、hexo init、npm install 和 npm install hexo-deployer-git）
>  4. git branch查看当前分支，默认为master，git checkout hexo切换分支，然后git add --all， git commit ，git push origin hexo（本例hexo为核定代码，master为发布代码，可按自己喜欢设置）
>  5. 修改_config.yml的deploy参数，分支为master
>  6. 执行hexo g -d生成网站并部署到github上


> **日常更新**
>  1. 修改博客后，git add --all， git commit ，git push origin hexo（如果在别的地方修改已提交到github上，执行git pull更新即可）
>  2. 执行hexo g -d发布网站到master分支上

**注意：**克隆已有核心代码后，执行npm install hexo、npm install、npm install hexo-deployer-git，不需要hexo init。


----------


## **拥有自己的github** ##

如果还没有github的账号就注册一个吧，[GitHub官网](http://www.github.com)
创建一个创库，creat new repository，
Repository name和自己的用户名相同。就填you_user_name.github.io。（you_user_name自行替换）
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p10.png)

## **设置sshkey** ##
```bash
ssh-keygen -t rsa -C "your_email@example.com"
#这将按照你提供的邮箱地址，创建一对密钥
```
按3个回车，密码为空
生成的时候会有一段这样的英文：
Enter file in which to save the key (/root/.ssh/id_rsa)：
我这里是默认路径，在/root/.ssh/ 文件夹里，当然你可以自己定义路径，回车就可以了

查看公钥，复制到你的github里。
```bash
cat id_rsa.pub
```

### **1. 登录github，进入你的 Account->Settings** ###

 ![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p7.jpg)

###  **2. 左侧栏选择SSH Keys** ###

###  **3. 复制粘贴** ###

 ![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p8.jpg)

###  **4. 测试下是否成功：** ###

```bash
ssh -T git@github.com
```
一般都会成功，出现以下提示
```bash
Hi XXXX! You've successfully authenticated, but GitHub does not provide shell access.
```

现在你已经可以通过SSH链接到GitHub了，还有一些个人信息需要完善的。
Git会根据用户的名字和邮箱来记录提交。GitHub也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的，名字必须是你的真名，而不是GitHub的昵称。
```bash
git config --global user.name "you_name"//用户名
git config --global user.email  "you@exmaple.com"//填写自己的邮箱
```
至此，ssh就设置完毕，如有问题，请参考[Git SSH Key 生成步骤](http://blog.csdn.net/hustpzb/article/details/8230454/)

## **部署到github** ##

### **1. 选择自己的仓库目录，即 you_user_name.github.io** ###

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p9.png)
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p11.png)
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p12.png)
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p13.png)
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p14.png)

###  **2. hexo的生成和部署** ###

 ```bash
cnpm install hero-deployer-git --save
hexo generate
hexo deploy
 ```
提示输入github的账号密码，输入密码即可。
注意，you_user_name.github.io 生效大约十分钟，请耐心等待。
hexo server 启动下，默认是4000端口
在浏览器中输入 you_host_name:4000（自行替换hostname）
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/hexo/p15.jpg)


----------


**至此，hexo+github部署完毕，enjoy it！**