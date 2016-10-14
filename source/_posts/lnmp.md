---
title: lnmp安装
date: 2016-04-13 16:18:10
tags: 
- linux
- lnmp
categories:
- 学习相关
---
<Excerpt in index | 首页摘要> 
# **系统需求** #
+ CentOS/RHEL/Fedora/Debian/Ubuntu/Raspbian Linux系统需求
+ 需要3GB以上硬盘剩余空间
+ 128M以上内存,Xen的需要有SWAP,OpenVZ的另外至少要有128MB以上的vSWAP或突发内存(小内存请勿使用64位系统),MySQL 5.6及MariaDB 10必须1G以上内存。
+ VPS或服务器必须已经联网，且必须设置的是网络源不能是光盘源，同时VPS/服务器 DNS要正常！
+ Linux下区分大小写，输入命令时请注意！
<!-- more -->
<The rest of contents | 余下全文>

# **安装步骤** #

## 下载并安装LNMP一键安装包 ##

可以选择使用下载版(推荐国外或者美国VPS使用)或者完整版(推荐国内VPS使用)，两者没什么区别，只是完整版把一些需要的源码文件预先放到安装包里。

安装LNMP执行：
```bash
wget -c http://soft.vpser.net/lnmp/lnmp1.2-full.tar.gz && tar zxf lnmp1.2-full.tar.gz && cd lnmp1.2-full && ./install.sh lnmp
```
如需要安装LNMPA或LAMP，将./install.sh 后面的参数替换为lnmpa或lamp即可。
如下载速度慢请更换其他下载节点

[下载页面](http://lnmp.org/download.html)
[LNMP下载节点具体替换方](http://lnmp.org/faq/lnmp-download-source.html)
[lnmp版本一览](http://soft.vpser.net/lnmp/)

这里选择最新稳定版，即lnmp 1.2
执行：
```bash
wget --no-check-certificate https://api.sinas3.com/v1/SAE_lnmp/soft/lnmp1.2-full.tar.gz && tar zxf lnmp1.2-full.tar.gz && cd lnmp1.2-full && ./install.sh lnmp  
```
> 按上述命令执行后，会出现如下提示：

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l1.png)
> 需要设置MySQL的root密码（不输入直接回车将会设置为root），输入后回车进入下一步，如下图:

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l2.png)
> 这里需要确认是否启用MySQL InnoDB，如果不确定是否启用可以输入 y ，输入 y 表示启用，输入 n 表示不启用。默认为y 启用，输入后回车进入下一步，选择MySQL版 

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l3.png)
> 输入MySQL或MariaDB版本的序号，回车进入下一步，选择PHP版本：

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l4.png)
> 输入PHP版本的序号，回车进入下一步，选择是否安装内存优化：

![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l5.png)
> 可以选择不安装、Jemalloc或TCmalloc，输入对应序号回车。
提示"Press any key to install...or Press Ctrl+c to cancel"后，按回车键确认开始安装。 
LNMP脚本就会自动安装编译Nginx、MySQL、PHP、phpMyAdmin、Zend Optimizer这几个软件。
安装时间可能会几十分钟到几个小时不等，主要是机器的配置网速等原因会造成影响。

## 安装完成 ## 
如果显示Nginx: OK，MySQL: OK，PHP: OK
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/lnmp/l6.png)
并且Nginx、MySQL、PHP都是running，80和3306端口都存在，并Install lnmp V1.2 completed! enjoy it.的话，说明已经安装成功。
接下来按添加虚拟主机教程，通过sftp或ftp服务器上传网站，将域名解析到VPS或服务器的IP上，解析生效即可使用。
[添加虚拟机教程](http://lnmp.org/faq/lnmp-vhost-add-howto.html)

---

至此，lnmp安装完毕

---
参考文献:
[LNMP一键安装包](http://lnmp.org/)
