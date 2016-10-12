---
title: sshfs-挂载远程目录
tags:
  - ssh
  - sshsf
categories:
  - ssh
  - sshfs
date: 2016-09-19 21:56:44
---
<Excerpt in index | 首页摘要> 
> 前言
 在``Linux``下我们通常使用``ssh``命令来登录远程``Linux``服务器，如果需要登录的远程服务器不止一个，来回切换的过程将会变得非常麻烦。如果使用``sshfs``，将可以直接将远程目录映射到本地，而不需要修改远程计算机的设置。下面我们来看一下如何使用``sshfs``。

环境准备：
``ssh``
``sshfs``

<!-- more -->
<The rest of contents | 余下全文>

# Linux(ubuntu) #
## 环境配置 生成公钥秘钥 ##
```bash
# 安装ssh和sshfs
sudo apt-get install -y ssh
sudo apt-get install -y sshfs

# 生成ssh 默认在/home/[user]/ 生成.ssh文件夹 user为你的账号，root为 /root/
ssh-keygen
```
远程服务器和本地服务器都执行以上，生成ssh

## 配置ssh远程登录 ##
+ 将本地生成的``id_rsa.pub``内容写入到远程服务器的``authorized_keys``文件中
```bash
# 获取本地公钥内容
cat id_rsa.pub

# 到远程服务器上添加到authorized_keys文件中
echo "公钥的内容" >> authorized_keys
```
+ ssh user@host 测试登录远程服务器

## sshfs ##
```bash
# 远程挂载 
# /data/tmp 是远程服务器想挂载的目录 
# /var/www 是本地目录（不存在需先创建）
sshfs -C -o reconnect user@host:/data/tmp /var/www/
```
FINISH~

# Mac #
## 安装brew ##
mac下使用``brew``管理
``brew``又叫``Homebrew``，是MacOSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，只需要一个命令， 非常方便

``brew``类似``ubuntu``系统下的``apt-get``的功能
``brew`` 的官方网站： http://brew.sh/   在官方网站对brew的用法进行了详细的描述
安装方法：  在Mac中打开Termal:  输入命令：
```bash
ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"
```
## 安装ssh、sshfs、osxfuse ##
``osxfuse ``[下载地址](https://sourceforge.net/projects/osxfuse/files/)
```bash
# 安装sshfs
brew install homebrew/fuse/sshfs
```

## 映射虚拟机文件夹到本地 ##
和``Ubuntu``一样
```bash
# 建议放到桌面上 方便
sshfs -C -o reconnect root@192.168.33.10:/home/wwwroot/default /Users/zjp/Desktop/Web
```
成功后在桌面上会有``osxfuse volumeo(sshfs)``这样一个文件。

ENJOY IT
