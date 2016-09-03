---
title: linux 基础
tags:
  - linux
categories:
  - linux
date: 2016-08-28 11:42:57
---
<Excerpt in index | 首页摘要> 
## 基本操作 ##

 按键 | 作用
 ---|----
 ``Ctrl+d`` | 键盘输入结束或退出终端
 ``Ctrl+s`` | 暂定当前程序，暂停后按下任意键恢复运行
 ``Ctrl+z`` | 将当前程序放到后台运行，恢复到前台为命令``fg``
 ``Ctrl+a`` | 将光标移至输入``行头``，相当于``Home``键
 ``Ctrl+e`` | 将光标移至输入``行末``，相当于``End``键
 ``Ctrl+k`` | 删除从光标所在位置到行末
 ``Alt+Backspace`` | 向前删除一个单词
 ``Shift+PgUp`` | 将终端显示向上滚动
 ``Shift+PgDn`` | 将终端显示向下滚动

<!-- more -->
<The rest of contents | 余下全文>

## 用户及文件权限管理 ##
### 查看用户###

```bash
$ who

#或者

$ who am i

$ root     pts/6        2016-08-28 11:53 (111.111.11.11)

#当前所在目录
$ pwd
```
``root``：当前用户
``pts/6``：pts/0 后面那个数字就表示打开的伪终端序号
``2016-08-28 11:53``：当前伪终端的启动时间
``111.111.11.11``：打开伪终端的ip

``who`` 命令其它常用参数

参数 | 说明
---|----
-a | 	打印能打印的全部
-d | 	打印死掉的进程
-a | 	同``am i``,``mom likes``
-q | 	打印当前登录用户数及用户名
-u | 	打印当前登录用户登录信息
-r | 	打印运行等级

### 创建用户###

``su <user>``可以切换到用户user，执行时需要输入目标用户的密码
``sudo <cmd>``可以以特权级别运行cmd命令，需要当前用户属于sudo组，且需要输入当前用户密码
创建一个用户peter，默认同时也为新用户创建home目录（/home/peter）：
```bash
$ sudo adduser peter
```
``-c`` comment 指定一段注释性描述。
``-d`` 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
``-g`` 用户组 指定用户所属的用户组。
``-G`` 用户组，用户组 指定用户所属的附加组。
``-s`` Shell文件 指定用户的登录Shell。
``-u`` 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。
**日常用法：**
```bash
#创建peter用户 指定登录shell是/home/data 所属group用户组 同事又属于adm和sudo用户组 group为主组
$ sudo useradd -s /home/data -g group -G adm,sudo peter
```

### 用户组 ###

#### 查看用户组 ####
+ 方法一

```bash
$ groups peter
peter : peter
```
冒号之前表示用户，后面表示该用户所属的用户组
（若创建不指定，默认自动创建一个与用户名相同的用户组）

+ 方法二

```bash
$ cat /etc/group | grep "peter"
#group_name:password:GID:user_list
peter:x:5000:
```
#### 将其他用户加入sudo用户组 ####

```bash
$ sudo usermod -G sudo peter
```

#### 删除用户 ####

```bash
$ sudo deluser peter --remove-home
```

### 文件权限 ###
```bash
$ ls -l
#文件类型和权限 链接数 所有者 所属用户 文件大小 最后修改时间 文件名
drwx-w-r-- 2 peter peter 4096 11月 6 15:30 test
```
权限：
rwx     -w-          r--
拥有者  所属用户组   其他用户
+ ``r`` 表示允许读权限
+ ``w`` 表示允许写权限
+ ``x`` 表示允许执行权限

表示文件类型：
+ ``d`` 目录
+ ``l`` 软链接
+ ``b`` 块设备
+ ``c`` 字符设备
+ ``s`` socket
+ ``p`` 管道
+ ``-`` 普通文件

#### 变更文件所有者 ####

```bash
$ ll
-rw-rw-r-- 1 root root 0 nov 20 11:14 test

$ sudo chown peter test
-rw-rw-r-- 1 peter root 0 nov 20 11:14 test
```

#### 修改文件权限 ####

```bash
$ ll
-rw-rw-r-- 1 peter root 0 nov 20 11:14 test

$ chmod 700 test
-rwx------ 1 peter root 0 nov 20 11:14 test
```

> 解释: ``700``分别代表，``拥有者``、 ``所属用户组``、 ``其他用户``3个的权限设置，0表示没有任何权限。
``r`` 4
``w`` 2
``x`` 1

## 文件打包与压缩 ##

### zip ###

#### zip压缩打包程序 ####

```bash
$ zip -r -[1-9] [-l] -q -o test.zip /home/test [-x ~/*.zip]
```

> 这里添加了一个参数用于设置压缩级别``-[1-9]``，1表示最快压缩但体积大，9表示体积最小但耗时最久。
最后那个``-x``是为了排除我们上一次创建的 zip 文件，否则又会被打包进这一次的压缩文件中，注意:这里只能使用绝对路径，否则不起作用。
``-r`` 递归目录打包
``-q`` 安静模式，不输出打包信息
``-o`` 表示输出文件，需在其后紧跟打包输出文件名
``-l`` 兼容windows的回车换行，如果要在Windows中打开，需要加上，否则可能会出现没有换行

#### unzip解压缩zip文件 ####

```bash
$ unzip -q [-l] [-O GBK] 中文压缩文件.zip
```
> 
``-l`` 只查看压缩包内容，不解压
``-O GBK`` 兼容中文编码，若是windows上创建的压缩文件，可能会出现中文乱码问题

### tar打包工具 ###

```bash
#打包压缩
$ tar -czf test.tar.gz /home/test

#解包解压
$ tar -xzf test.tar.gz
```

## Linux任务计划crontab ##

> ``crontab`` 储存的指令被守护进程激活，``crond`` 为其守护进程，``crond`` 常常在后台运行，``每一分钟``会检查一次是否有预定的作业需要执行。
用于设置``周期性``被执行的指令，通过``crontab``命令，我们可以在固定的间隔时间执行指定的系统指令或shell　script脚本。时间间隔的单位可以是分钟、小时、日、月、周的任意组合。

```bash
#添加一个计划任务，会跳到计划文件去
$ crontab -e
```

``crontab``文档编辑的格式参数

代表意义 | 分钟 | 小时 | 日期 | 月份 | 周 | 指令
   ----	 | ---- | ---- | ---- | ---- |----| ----
数字范围 | 0-59 | 0-23 | 1-31 | 1-12 | 0-7| touch test

> 周的数字为``0``或``7``时，都代表``星期天``

特殊字符 | 代表意义
   ----   | ----
``*``        | 代表任何时刻都接受，日和月周都是*，则代表不论何月何日星期几的时刻都执行指令
``,``		 | 代表分隔时段，如果要3:00和6:00执行，则 0 3,6 * * * command
``-``		 | 代表一段时间范围内，若8点到12点之间没20分钟执行，则 20 8-12 * * * command
``/n``       | n代表每个n个单位执行，若每五分钟执行一次，则 */5 * * * * command

在文档的最后一排加上这样一排命令,该任务是每分钟我们会在/home/shiyanlou目录下创建一个以当前的年月日时分秒为名字的空白文件
```bash
*/1 * * * * touch /home/shiyanlou/$(date +\%Y\%m\%d\%H\%M\%S)
```
> 注意 “ % ” 在 ``crontab``文件中，有结束命令行、换行、重定向的作用，前面加”\”符号转意，否则，“%”符号将执行其结束命令行或者换行的作用，并且其后的内容会被做为标准输入发送给前面的命令。


```bash
# 查看我们添加了哪些任务
crontab -l

# 查看cron的守护进程是否启动
ps aux | grep cron

# 开启了rsyslog可以查看执行log（sudo service rsyslog start）
sudo tail -f /var/log/syslog

# 删除任务
crontab -r
```
> ``crontab -e`` 是针对使用者的 cron 來设计的，也就是每个用户在添加任务，就会在 ``/var/spool/cron/crontabs``中添加一个该用户自己的任务文档，这样可以做到隔离，独立，不会混乱。

```bash
# 系统例行任务，直接编辑/etc/crontab
$ ll /etc/ | grep cron
```
+ ``/etc/cron.daily``，目录下的脚本会每天让执行一次，在每天的6点25分时运行
+ ``/etc/cron.hourly``，目录下的脚本会每个小时让执行一次，在每小时的17分钟时运行
+ ``/etc/cron.mouthly``，目录下的脚本会每月让执行一次，在每月1号的6点52分时运行
+ ``/etc/cron.weekly``，目录下的脚本会每周让执行一次，在每周第七天的6点47分时运行

更深入学习crontab：[鸟哥私房菜](http://linux.vbird.org/linux_basic/0430cron.php)

## netstat ##

``Netstat`` 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (MulticastMemberships) 等等。

执行netstat后
```bash
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address Foreign Address State
tcp 0 2 210.34.6.89:telnet 210.34.6.96:2873 ESTABLISHED
tcp 296 0 210.34.6.89:1165 210.34.6.84:netbios-ssn ESTABLISHED
tcp 0 0 localhost.localdom:9001 localhost.localdom:1162 ESTABLISHED
tcp 0 0 localhost.localdom:1162 localhost.localdom:9001 ESTABLISHED
tcp 0 80 210.34.6.89:1161 210.34.6.10:netbios-ssn CLOSE

Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags Type State I-Node Path
unix 1 [ ] STREAM CONNECTED 16178 @000000dd
unix 1 [ ] STREAM CONNECTED 16176 @000000dc
unix 9 [ ] DGRAM 5292 /dev/log
unix 1 [ ] STREAM CONNECTED 16182 @000000df
```

> 从整体上看，netstat的输出结果可以分为两个部分：
一个是Active Internet connections，称为有源TCP连接，其中"Recv-Q"和"Send-Q"指%0A的是接收队列和发送队列。这些数字一般都应该是0。如果不是则表示软件包正在队列中堆积。这种情况只能在非常少的情况见到。
另一个是Active UNIX domain sockets，称为有源Unix域套接口(和网络套接字一样，但是只能用于本机通信，性能可以提高一倍)。
Proto显示连接使用的协议,RefCnt表示连接到本套接口上的进程号,Types显示套接口的类型,State显示套接口当前的状态,Path表示连接到套接口的其它进程使用的路径名。

### 常见参数 ###

+ ``-a`` (all)显示所有选项，默认不显示LISTEN相关
+ ``-t`` (tcp)仅显示tcp相关选项
+ ``-u`` (udp)仅显示udp相关选项
+ ``-n`` 拒绝显示别名，能显示数字的全部转化成数字。
+ ``-l`` 仅列出有在 Listen (监听) 的服務状态
+ ``-p`` 显示建立相关链接的程序名
+ ``-r`` 显示路由信息，路由表
+ ``-e`` 显示扩展信息，例如uid等
+ ``-s`` 按各个协议进行统计
+ ``-c`` 每隔一个固定时间，执行该netstat命令。

提示：``ISTEN``和LISTENING``的状态只有用``-a``或者``-l``才能看到

### 日常用法 ###

#### 列出所有端口 (包括监听和未监听的) ####

** 列出所有端口 netstat -a **

```bash
# netstat -a | more
 Active Internet connections (servers and established)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 udp        0      0 *:bootpc                *:*
 
Active UNIX domain sockets (servers and established)
 Proto RefCnt Flags       Type       State         I-Node   Path
 unix  2      [ ACC ]     STREAM     LISTENING     6135     /tmp/.X11-unix/X0
 unix  2      [ ACC ]     STREAM     LISTENING     5140     /var/run/acpid.socket
```

** 列出所有 tcp 端口 netstat -at **
```bash
# netstat -at
 Active Internet connections (servers and established)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 tcp        0      0 localhost:ipp           *:*                     LISTEN
 tcp        0      0 *:smtp                  *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
 ```
#### 列出所有处于监听状态的 Sockets ####
** 只显示监听端口 netstat -l **
```bash
# netstat -l
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:ipp           *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
 udp        0      0 *:49119                 *:*
 ```
** 只列出所有监听 tcp 端口 netstat -lt **
```bash
# netstat -lt
 Active Internet connections (only servers)
 Proto Recv-Q Send-Q Local Address           Foreign Address         State
 tcp        0      0 localhost:30037         *:*                     LISTEN
 tcp        0      0 *:smtp                  *:*                     LISTEN
 tcp6       0      0 localhost:ipp           [::]:*                  LISTEN
 ```

 参考：[Linux netstat命令详解](http://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316661.html)
