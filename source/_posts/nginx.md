---
title: nginx
tags:
  - 学习相关
  - 分享
categories:
  - nginx
date: 2016-08-11 19:57:18
---
<Excerpt in index | 首页摘要> 
``nginx``是一个轻量级且很强大的高性能``web``和``反向代理``服务器。一些干货：
[Nginx 工作原理和优化、漏洞](http://blog.jobbole.com/103548/)
[nginx配置详解](http://blog.csdn.net/xmtblog/article/details/42295181)
[什么是CGI、FastCGI、PHP-CGI、PHP-FPM、Spawn-FCGI？](http://www.mike.org.cn/articles/what-is-cgi-fastcgi-php-fpm-spawn-fcgi/)
[NGINX FastCGI模块（FastCGI） ](http://blog.163.com/passc_lee/blog/static/2152541462013593345604/)
[nginx重写：如果$uri 不是文件不是目录 跳转到$uri/](https://segmentfault.com/q/1010000002413203)
[nginx用户认证配置（ Basic HTTP authentication）](http://www.ttlsa.com/nginx/nginx-basic-http-authentication/)
[Nginx日志管理和切割](http://studys.blog.51cto.com/9736817/1665922?sukey=3997c0719f151520f8595d5cb006801f898ab0de7de9b65a4b580ae34cd3043b4bb4362c935523e46a2c0487a5c759aa)

---

# ** nginx 基础篇 ** #

## 期望 ##
- 做到能够安装配置``nginx+php``，知道基本的``nginx``核心配置选项
- 知道``server``/``fastcgi_pass``/``acesss_log``等基础配置
- 能够让``nginx+php-fpm``顺利工作

<!-- more -->
<The rest of contents | 余下全文>
### Nginx+FastCGI运行原理 ###
- ``Nginx``在启动后，会有一个``master``进程和多个``worker``进程。
- ``master``进程充当这个进程组与用户的交互接口，同时对进程进行监护。管理``worker``进程来实现重启服务、平滑升级、更换日志文件、配置文件实时生效等功能。
- ``master``进程在接到信号后，会先重新加载配置文件，然后再启动新的``worker``进程，并向所有老的``worker``进程发送信号，告诉他们可以光荣退休了。新的``worker``在启动后，就开始接收新的请求，而老的``worker``在收到来自``master``的信号后，就不再接收新的请求，并且在当前进程中的所有未处理完的请求处理完成后，再退出。
- ``Nginx``不支持对外部程序的直接调用或者解析，所有的外部程序（包括``PHP``）必须通过``FastCGI``接口来调用。``FastCGI``接口在Linux下是``socket``（这个socket可以是文件socket，也可以是ip socket）。

![](http://7xsuc5.com1.z0.glb.clouddn.com/image/nginx/n1.png)

### Nginx+PHP-FPM ###
- ``PHP-FPM``是管理``FastCGI``的一个管理器,相对``Spawn-FCGI``，``PHP-FPM``在CPU和内存方面的控制都更胜一筹，而且前者很容易崩溃。
- ``FastCGI`` 的主要优点是把``动态语言``和``HTTP Server``分离开来，所以``Nginx``与``PHP/PHP-FPM``经常被部署在不同的服务器上，以分担前端``Nginx``服务器的压力，使``Nginx``专一处理静态请求和转发动态请求，而``PHP/PHP-FPM``服务器专一解析``PHP``动态请求。

### Nginx+PHP配置 ##
以下为简单web服务器配置，使用``php-fpm``通信，支持``php``。
值得一提的是，``nginx``和``PHP/PHP-FPM``通信有两种方式：
- ``unix socket``方式(``文件socket``)：消耗资源少，速度快，但不稳定，并发大时容易发生错误且不会返回异常。
- ``TCP``方式（``IP socket``）：TCP是使用TCP端口连接``127.0.0.1:9000``，需要经过本地回环驱动，申请临时端口和资源，但相对的，保证通信正确性和完整性。

```nginx
server {
        listen 80;
        server_name  your_server_name.com;
        root /usr/share/nginx/html/;
        index index.html index.php index.htm;
                        
        location / {
            try_files $uri $uri/ /index.php;                            
        }

        location ~ .php$ {
            try_files $uri = 404;
            include fastcgi_params;
            #使用unix socket方式和nginx通信
            fastcgi_pass unix:/var/run/php5-fpm.sock; 
           #使用tcp方式和nginx通信
           #fastcgi_pass 127.0.0.1:9000;  
        }
}
```
