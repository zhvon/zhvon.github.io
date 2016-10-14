---
title: socket编程学习
tags:
  - socket
  - linux
categories:
  - 学习相关 
date: 2016-04-18 10:40:20
---
<Excerpt in index | 首页摘要> 
如果对``socket``还没有很好的理解
可以干了这篇干货：[Linux的SOCKET编程详解](http://blog.csdn.net/hguisu/article/details/7445768/)

# socket中几个重要的结构体 #

## IP地址信息结构体 ##

此数据结构用做 bind、connect、recvmsg、recvmsg等函数的参数，指明地址信息。但一般编程中并不直接针对此数据结构操作，而是使用另一个与``sockaddr``等价的数据结构（``sockaddr_in``，``sockaddr``)

```c
struct sockaddr {
        unsigned short  sa_family;      /* address family, AF_xxx */
        char  sa_data[14];    /* 14 bytes of protocol address */
    };
```
<!-- more -->
<The rest of contents | 余下全文>
 
| sa_family(地址族,也就是 IP 地址类型)  |      解释           |
| :---: |:-------------:|
| AF_INET | AF 是"Address Family"的简写，INET是"Inetnet"的简写。AF_INET 表示 IPv4 地址，例如 127.0.0.1      |
| AF_INET6 | 表示 IPv6 地址，例如 1030::C9B4:FF12:48AA:1A2B      |
| AF_UNSPEC | 协议无关      |


## IPV4地址信息结构体 ##
```c
struct sockaddr_in {
    short int           sin_family;     /* Address family; must be AF_INET. */
    unsigned short int  sin_port;       /* Port number */
    struct in_addr      sin_addr;       /* Internet address */
    unsigned char       sin_zero[8];    /* Same size as struct sockaddr */ //是为了让 sockaddr 与 socketaddr_in 两个数据结构保持大小相同而保留的空字节
};

struct in_addr {
    uint32_t s_addr; //ip地址 通过 inet_addr(ipstr) 将一个点分十进制的IP(eg:192.168.1.101)转换成一个长整数型数
};
```

## IPV6地址信息结构体 ##
```c
struct sockaddr_in6 {
    short               sin6_family;        /* Address family; must be AF_INET6. */
    u_short             sin6_port;          /* Transport-level port number */
    u_long              sin6_flowinfo;      /* Ipv6 flow information. */
    struct in6_addr     sin6_addr;          /* Ipv6 address. */
    u_long              sin6_scope_id;      /* Set of interfaces for a scope. */

};

struct in6_addr {
    union {
    u_char      Byte[16];       /* Host address formatted as 16 u_chars. */
    u_short     Word[8];        /* Host address formatted as 8 u_shorts. */
    } u;

};
```


## addrinfo结构体 ##
```c
struct addrinfo {
    int                 ai_flags;       /* customize behavior */
    int                 ai_family;      /* address family */
    int                 ai_socktype;    /* socket type */
    int                 ai_protocol;    /* protocol */
    size_t              ai_addrlen;     /* length in bytes of address */
    struct sockaddr     *ai_addr;       /* address */
    char                *ai_canonname;  /* canonical name of host */
    struct addrinfo     *ai_next;       /* next in list */

};
```
| ai_flags  |      解释           |
| :---: |:-------------:|
| AI_ADDRCONFIG | 查询配置的地址类型（IPV4或IPV6）      |
| AI_ALL | 查找IPV4和IPV6地址      |
| AI_PASSIVE | 套接字地址用于监听绑定      |
| AI_CANONNAME | 用于返回主机的规范名称      |
| AI_NUMERICHOST | 地址为数字串      |

** ``ai_protocol `` **

协议（``Protocol``）就是网络通信的约定，通信的双方必须都遵守才能正常收发数据。协议有很多种，例如 ``TCP``、``UDP``、``IP`` 等，通信的双方必须使用同一协议才能通信。协议是一种规范，由计算 机组织制定，规定了很多细节，例如，如何建立连接，如何相互识别等。

协议仅仅是一种规范，必须由计算机软件来实现。例如 IP 协议规定了如何找到目标计算机，那么各个开发商在开发自己的软件时就必须遵守该协议，不能另起炉灶。

所谓协议族（``Protocol Family``），就是一组协议（多个协议）的统称。最常用的是 ``TCP/IP`` 协议族，它包含了 ``TCP、IP、UDP、Telnet、FTP、SMTP`` 等上百个互为关联的协议，由于`` TCP、IP`` 是两种常用的底层协议，所以把它们统称为``TCP/IP`` 协议族。


| ai_socktype（数据传输协议）  |      解释           |
| :---: |:-------------:|
| IPPROTO_IP | IP协议      |
| IPPROTO_IPV4 | IPv4 |
| IPPROTO_IPV6 | IPv6 |
| AI_PASSIVE | 套接字地址用于监听绑定      |
| IPPROTO_UDP | UDP 传输协议      |
| IPPROTO_TCP | TCP 传输协议      |
---
| ai_socktype（数据传输方式）  |      解释           |
| :---: |:-------------:|
| SOCK_STREAM | 表示面向连接的数据传输方式。      |
| SOCK_DGRAM | 表示无连接的数据传输方式 |

## socket中使用到的网络字节序 ##
不同机器保存和解析数据的方式不同（主流的Intel系列为小端序），小端序系统和大端序系统通信时会发生数据解析错误。因此在发送数据前，要将数据转换为统一的格式 -- ``网络字节序（Network Byte Order）``。``网络字节序统一为大端序``。
比如说，主机A 先把数据转换成 大端序 再进行 网络传输，主机B 收到数据后先转换为 自己的格式 再解析，

如下为初始化sockaddr_in 
```c
//创建sockaddr_in结构体变量
struct sockaddr_in serv_addr;
memset(&serv_addr, 0, sizeof(serv_addr));  //每个字节都用0填充
serv_addr.sin_family = AF_INET;  //使用IPv4地址
serv_addr.sin_addr.s_addr = inet_addr("127.0.0.1");  //具体的IP地址
serv_addr.sin_port = htons(1234);  //端口号
```
htons() 用来将 当前主机字节序 转换为 网络字节序，其中h代表主机（host）字节序，n代表网络（network）字节序，s代表short，htons 是 h、to、n、s 的组合，可以理解为 "将short型数据从当前主机字节序转换为网络字节序"。

---

常见的网络字节转换函数有：

``htons()``：host to network short，将short类型数据从主机字节序转换为网络字节序。

``ntohs()``：network to host short，将short类型数据从网络字节序转换为主机字节序。

``htonl()``：host to network long，将long类型数据从主机字节序转换为网络字节序。

``ntohl()``：network to host long，将long类型数据从网络字节序转换为主机字节序。

# 编写一个简易服务器# 

## 例子代码 ##

```c++
/*************************************************************************
     > File Name: hello_server.cpp
     > Author: zhvon
     > Blog: http://blog.zhvon.com
     > Mail: wind-catalpa@.com
     > Created Time: 2016-04-19 五 13:23:17 CST
************************************************************************/

#include <iostream>
#include <errno.h>
#include <cstdlib>
#include <cstdio>
#include <cstring>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
using namespace std;
#define PORT 8080 // 端口

int main() {
    int socket_fd, connect_fd; // 服务器socket，客户端连接socket
    struct sockaddr_in serveraddr; // 服务器地址

    if((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == -1) { // TCP，三次握手。
         printf("create socket error: %s\n", strerror(errno));
         exit(0);
         }
    memset(&serveraddr, 0, sizeof(serveraddr));

    serveraddr.sin_family = AF_INET; // IPV4
    serveraddr.sin_addr.s_addr = htonl(INADDR_ANY); // 0.0.0.0
    serveraddr.sin_port = htons(PORT); // 主机字节码 => 网络字节码

    if(bind(socket_fd, (sockaddr*)&serveraddr, sizeof(serveraddr)) == -1) {
        printf("bind socket error: %s\n", strerror(errno));
        exit(0);
        }

    if(listen(socket_fd, 100) == -1) { // 监听，最大连接数
         printf("listen socket error: %s", strerror(errno));
         exit(0);
         }

    // HTTP
    const char status[] = "HTTP/1.0 200 OK\r\n";
    const char header[] = "Server: Netcan Web Server\r\nContent-Type: text/html\r\n\r\n";
    const char body[] = "<html><title>Hello World!</title></head><body><h2>Hello World!</h2></body></html>";

    while(true) {
         if((connect_fd = accept(socket_fd, (sockaddr*)NULL, NULL)) == -1) { // 阻塞进程
             printf("accept socket error: %s\n", strerror(errno));
             continue;
         }

         // 回应数据
         if(!fork()) {
             send(connect_fd, status, sizeof(status), 0);
             send(connect_fd, header, sizeof(header), 0);
             send(connect_fd, body, sizeof(body), 0);
             exit(0);
                                                                                                                        
         }
         close(connect_fd);
                                                                       
     }
    close(socket_fd);
    return 0;

}

```
## 编译,访问ip:端口 ##
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/sockey/s1.jpg)
![](http://7xsuc5.com2.z0.glb.clouddn.com/image/sockey/s2.jpg)

