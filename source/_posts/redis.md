---
title: Redis基础
tags:
  - redis
  - 缓存
categories:
  - redis
date: 2016-09-03 10:54:27
---
<Excerpt in index | 首页摘要> 
# Redis简介和安装 #

> ``Redis``是一个由Salvatore Sanfilippo写的``key-value``存储系统，常被称作是一款数据结构服务器（data structure server）。

``Redis``的优点
+ 性能极高 - Redis能支持超过 ``100K+`` 每秒的读写频率。
+ 丰富的数据类型 – ``Redis``支持二进制案例的 ``Strings``, ``Lists``, ``Hashes``, ``Sets`` 及 ``Ordered Sets`` 数据类型操作。
+ 原子 – Redis的所有操作都是``原子性``，同时Redis还支持对几个操作全并后的原子性执行。
+ 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

``Redis``和``Memcached``的区别：
+ Redis支持数据操作，相对于memcache不仅有k-v的结构，还提供很多string list hash等数据结构
+ 内存使用效率，单kv的方式memcache更高。但若redis采用hash结构做kv存储，组合式压缩，利用率略胜一筹。
+ 性能。redis单核单线程，memcache多核多线程。单核小数据时redis>memcache。100k以上的数据redis<memcache。
+ Redis数据持久化操作，memcache则不然
+ 在Redis中，并不是所有的数据都一直存储在内存中的。

<!-- more -->
<The rest of contents | 余下全文>

## Redis安装 ##

```bash
# 获取Redis安装包
$ wget wget http://labfile.oss.aliyuncs.com/files0422/redis-2.8.9.tar.gz

# 解压Redis安装包
$ tar -zxf redis-2.8.9.tar.gz

# 进入解压目录 进行编译
$ cd redis-2.8.9
$ make
```
安装成功后运行测试，看``Redis``是否正常
```bash
$ make test

############## 如果有如下提示
You need tcl 8.5 or newer in order to run the Redis test

$ sudo apt-get install -y tcl8.6
```
根据机子的配置大概5~10分钟，最后提示``All tests passed without errors!``证明测试通过

## 启动Redis ##
```bash
$ ls
$ cd src
$ ls
```
> 在 Redis 安装完成后，注意一些重要的文件，可用 ls 命令查看。服务端：``src/redis-server``，客户端：``src/redis-cls``，默认配置文件：``redis.conf``

通过将执行文件放置在``$PATH``环境目录下，便于以后执行可不输入完整路径
```bash
$　cp redis-server /usr/local/bin/
$　cp redis-cli /usr/local/bin/

# 启动 Redis-server
$ redis-server
```

** 说明 **：可以发现启动的端口（``Port``）为缺省的6379和进程id（``PID``）. 用户可以在启动的时候，指定具体的配置文件，并在其中指定启动的端口。

```bash
# 查看 Redis
$ ps -ef | grep redis

# 通过启动命令检查Redis服务器状态
$ netstat -nlt|grep 6379

# 启动 Redis-client
$ redis-cli
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> 
```

至此，redis安装完成。

***

# Redis数据类型 #

``Redis strings``：Redis字符串是二进制安全的，这意味着一个Redis字符串能包含``任意类型的数据``，例如：一张JPEG格式的图片或者一个序列化的Ruby对象。一个字符串类型的值最多能存储512M字节的内容。
``Redis Lists``：Redis列表是简单的字符串列表，按照插入顺序排序。
``Redis Hashes``：``字符串字段``和``字符串值``之间的``映射``，因此他们是展现对象的完美数据类型。一个带有一些字段的hash仅仅需要一块很小的空间存储因此你可以存储数以百万计的对象在一个小的Redis实例中。
``Redis 无序集合``：是一个无序的字符串集合. 你可以以``O(1)``的时间复杂度完成``添加``，``删除``，以及``测试元素是否存在``。Redis 集合拥有令人满意的``不允许``包含``相同``成员的属性。``多次添加相同``的元素，最终在集合里``只会有一个``元素。
``Redis 有序集合``：Redis有序集合与普通集合非常相似，是一个``没有重复元素``的字符串集合。成员都关联了一个``评分``，这个评分被用来按照``从最低分到最高分``的方式``排序``集合中的成员。集合的``成员是唯一``的，但是``评分可以是重复``了。
