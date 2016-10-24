---
title: Apache web服务器简单配置
tags:
  - linux
  - apache
  - 虚拟主机
categories:
  - apache
  - linux
date: 2016-10-17 12:06:29
---
<Excerpt in index | 首页摘要> 

# Apache安装 #
```bash
# Ubuntu 14.0 LTS
apt-get install -y apache2

/etc/init.d/apache2 restart
```
<!-- more -->
<The rest of contents | 余下全文>

# Apache配置基于域名虚拟主机（多个站点） #

step：
1、在``/etc/apache2/sites_available``目录下新增``test.conf``配置文件
2、配置``test.conf``文件
3、在``/var/www/`` 目录下新增网站目录（站点） test（``/var/www/test/``）
4、利用``a2ensite``使``test``站点生效，``test.conf``在``/etc/apache2/sites_enabled``中生成链接文件
5、重启``apache``

```bash
cd /etc/apache2/sites_available/

vim test.conf

mkdir /var/www/test

sudo a2ensite test

ls -asl /etc/apache2/sites_enabled

/etc/init.d/apache2 restart
```

> .conf 文件详细配置
往后新建站点直接copy 替换``test`` 为新站点名即可
配置完成后 访问 ``test.example.com`` 测试


```apache
ServerName www.example.com
ServerAlias test.example.com

ServerAdmin webmaster@localhost
DocumentRoot /var/www/test

<Directory "/var/www/test">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride All
    Order allow,deny
    allow from all
</Directory>

ErrorLog ${APACHE_LOG_DIR}/test/error.log
CustomLog ${APACHE_LOG_DIR}/test/access.log combined
```

enjoy it..