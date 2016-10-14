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

# Redis数据类型 #

``Redis strings``：Redis字符串是二进制安全的，这意味着一个Redis字符串能包含``任意类型的数据``，例如：一张JPEG格式的图片或者一个序列化的Ruby对象。一个字符串类型的值最多能存储512M字节的内容。
``Redis Lists``：Redis列表是简单的字符串列表，按照插入顺序排序。
``Redis Hashes``：``字符串字段``和``字符串值``之间的``映射``，因此他们是展现对象的完美数据类型。一个带有一些字段的hash仅仅需要一块很小的空间存储因此你可以存储数以百万计的对象在一个小的Redis实例中。
``Redis 无序集合``：是一个无序的字符串集合. 你可以以``O(1)``的时间复杂度完成``添加``，``删除``，以及``测试元素是否存在``。Redis 集合拥有令人满意的``不允许``包含``相同``成员的属性。``多次添加相同``的元素，最终在集合里``只会有一个``元素。
``Redis 有序集合``：Redis有序集合与普通集合非常相似，是一个``没有重复元素``的字符串集合。成员都关联了一个``评分``，这个评分被用来按照``从最低分到最高分``的方式``排序``集合中的成员。集合的``成员是唯一``的，但是``评分可以是重复``了。

## Redis strings ##

常用方法 | 解释 | 返回值
---		 | ---  | ---
``set``		 | set key value [nx or xx]。设置对应key的value默认采用xx参数。``nx`` 当有``相同``的key，set会``失败``。``xx`` 覆盖现有的key对应的value。| 成功返回``OK``
``get``		 | get key 获取key对应的value值 |成功返回对应key的value值，不存在返回``nil``
``incr``	 | incr key。让the value 成为一个整数，运行一次INCR便+1 | 返回运算后的值
``incrby``   | incrby key n(为大于0的整数) 。INCRBY命令便是一个加法运算 | 返回运算后的值
``mset``	 | 一次``设置``多个key的value。mset key1 value1 key2 value2 key3 value3 | 成功返回ok
``mget``	 | 一次``获取``多个key的value。mget key1 key2 key3	| 返回key对应的value，不存在显示nil

```bash
############### set and get ############# 
127.0.0.1:6379> set mykey newval
OK
127.0.0.1:6379> get mykey
"newval"
127.0.0.1:6379> set mykey sole nx
(nil)
127.0.0.1:6379> set mykey sole xx
OK
127.0.0.1:6379> get mykey
"sole"
127.0.0.1:6379> set mykey fude
OK
127.0.0.1:6379> get mykey
"fude"

 ############## incr and incrby ##############
127.0.0.1:6379> set count 100
OK
127.0.0.1:6379> get count
"100"
127.0.0.1:6379> INCR count
(integer) 101
127.0.0.1:6379> INCR count
(integer) 102
127.0.0.1:6379> INCRBY count 50
(integer) 152

################## mset and mget #################
127.0.0.1:6379> MSET a 1 b 2 c 3
OK
127.0.0.1:6379> MGET a b c
1) "1"
2) "2"
3) "3"
```

## Redis Lists ##

常用方法 | 解释 | 返回值
---		 | ---  | ---
``rpush``	 | ``rpush list a``。插入一个新元素到``尾部``,可以``一次插入多个``元素 | 返回当前元素个数
``lpush``	 | ``lpush list b``。插入一个新元素到``头部``,可以``一次插入多个``元素 | 返回当前元素个数
``lrange``	 | ``lrange list 0 -1``。利用了两个检索值打印，0表示list的开头第一个，-1表示list的倒数第一个，即最后一个 | 打印对应下标的元素
``rpop``	 | ``rpop list``。从``尾部取出``元素 | 返回所取出的元素
``lpop``	 | ``lpop list``。从``头部取出``元素 | 返回所取出的元素
``del``     | ``del list``。删除集合 | 成功返回1，否则返回0

```bash
################## rpush 、lpush and lrange ##################
127.0.0.1:6379> rpush mylist a
(integer) 1
127.0.0.1:6379> rpush mylist b
(integer) 2
127.0.0.1:6379> rpush mylist three
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "a"
2) "b"
3) "three"
127.0.0.1:6379> LRANGE mylist 0 -2
1) "a"
2) "b"
127.0.0.1:6379> rpush mylist 1 2 3
(integer) 6
127.0.0.1:6379> LRANGE mylist 0 -1
1) "a"
2) "b"
3) "three"
4) "1"
5) "2"
6) "3"

################### rpop lpop and del ##################
127.0.0.1:6379> RPOP mylist
"3"
127.0.0.1:6379> LRANGE mylist 0 -1
1) "a"
2) "b"
3) "three"
4) "1"
5) "2"
127.0.0.1:6379> lpop mylist 
"a"
127.0.0.1:6379> LRANGE mylist 0 -1
1) "b"
2) "three"
3) "1"
4) "2"
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> DEL mylist
(integer) 1
127.0.0.1:6379> LRANGE mylist 0 -1
(empty list or set)
127.0.0.1:6379> 
```

## Redis Hashes ##

常用方法 | 时间复杂度 | 解释 | 返回值
---		 | ---		  | ---	 | ---
``hset``	 | O(1) 	  | ``hset key field value``。为指定的Key设定``Field/Value``对，如果Key不存在，该命令将创建新Key以参数中的Field/Value对，如果参数中的Field在该Key中已经存在，则用新值覆盖其原有值。| ``1``表示新的``Field``被设置了新值，``0``表示Field已经``存在``，用新值覆盖原有值
``hsetnx``	 | O(1) 	  | ``hsetnx key field value``。只有当参数中的Key或Field不存在的情况下，为指定的Key设定``Field/Value``对，否则该命令不会进行任何操作 | ``1``表示新的Field被设置了新值，0表示Key或Field已经存在，该命令没有进行任何操作
``hget``	 | O(1) 	  | ``hget key field``。返回指定Key中指定Field的关联值。| 返回参数中Field的关联值，如果参数中的Key或Field不存，返回``nil``
``hmset``	 | O(N) 	  | ``hmset key field value [field value...]``。时间复杂度中的``N``表示被设置的Field数量。逐对依次设置参数中给出的``Field/Value``对。如果其中某个Field已经``存在``，则用新值``覆盖``原有值。如果Key``不存在``，则``创建``新Key，同时设定参数中的Field/Value | 
``hmget``	 | O(N) 	  | ``hmget key field [field...]``时间复杂度中的N表示请求的Field数量。获取和参数中指定Fields关联的一组Values。如果请求的Field``不存在``，其值返回``nil``。如果Key不存在，该命令将其视为空Hash，因此返回一组``nil``。| 返回和请求Fields关联的一组``Values``，其返回顺序等同于Fields的``请求顺序``
``hgetall``	 | O(N) 	  | ``hgetall key``时间复杂度中的N表示Key包含的Field数量。获取该键包含的所有``Field/Value``。其返回格式为一个Field、一个Value，并以此类推 | ``Field/Value``的列表。
``hkeys``	 | O(N) 	  | ``hkeys key`` 返回指定Key的所有``Fields``名 | ``Field``的列表
``hvals``	 | O(N) 	  | ``hvals key`` 返回指定Key的所有``Values``名 | ``Value``的列表
``hlen``	 | O(1)       | ``hlen key`` 获取该Key所包含的Field的数量 | 返回Key包含的``Field数量``，如果Key``不存在``，返回``0``
``hexists``  | O(1) 	  | ``hexists key field`` 判断指定Key中的指定Field是否存在 | ``1``表示存在，``0``表示参数中的Field或Key不存在

```bash
##################### hset hsetnx hget hmset hmget #####################
127.0.0.1:6379> hset myhash name peter
(integer) 1
127.0.0.1:6379> HGET myhash name
"peter"
127.0.0.1:6379> HMSET myhash sex 1 bir 1992
OK
127.0.0.1:6379> HMGET myhash name sex bir
1) "peter"
2) "1"
3) "1992"
127.0.0.1:6379> 
127.0.0.1:6379> HSETNX myhash name gwen
(integer) 0
127.0.0.1:6379> HGET myhash name
"peter"

##################### hgetall hkeys hvals hlen hexists #####################
127.0.0.1:6379> HGETALL myhash
1) "name"
2) "peter"
3) "sex"
4) "1"
5) "bir"
6) "1992"
127.0.0.1:6379> HKEYS myhash
1) "name"
2) "sex"
3) "bir"
127.0.0.1:6379> HVALS myhash
1) "peter"
2) "1"
3) "1992"
127.0.0.1:6379> HLEN myhash
(integer) 3
127.0.0.1:6379> 
127.0.0.1:6379> HEXISTS myhash name
(integer) 1
127.0.0.1:6379> HEXISTS myhash tall
(integer) 0
27.0.0.1:6379> DEL myhash
(integer) 1
127.0.0.1:6379> hkeys myhash
(empty list or set)
```

## Redis 无序集合(Redis-set) ##

应用范围：
> 1). 可以使用Redis的``Set``数据类型跟踪一些``唯一性``数据，比如访问某一博客的``唯一IP地址信息``。对于此场景，我们仅需在每次访问该博客时将访问者的IP存入Redis中，``Set``数据类型会自动保证``IP地址``的``唯一性``。
2). 充分利用Set类型的服务端``聚合操作``方便、高效的特性，可以用于维护数据对象之间的关联关系。比如所有购买某一电子设备的客户ID被存储在一个指定的Set中，而购买另外一种电子产品的客户ID被存储在另外一个Set中，如果此时我们想获取有哪些客户``同时``购买了这两种商品时，Set的intersections命令就可以充分发挥它的方便和效率的优势了。

常用方法 | 时间复杂度 | 解释 | 返回值
---		 | ---		  | ---	 | ---
``sadd`` 	 | O(N) 	  | ``sadd key member [member..]`` 时间复杂度中的N表示操作的成员数量。如果在插入的过程用，参数中有的成员在Set中已经存在，该成员将被忽略，而其它成员仍将会被正常插入。如果执行该命令之前，该Key并不存在，该命令将会创建一个新的Set，此后再将参数中的成员陆续插入。如果该Key的Value不是Set类型，该命令将返回相关的错误信息 | 本次操作实际插入的``成员数量``
``smembers`` | O(N) 	  | ``smembers key`` 获取与该Key关联的Set中所有的成员 | 返回Set中``所有的成员``
``sismember``| O(N) 	  | ``sismember key`` member判断参数中指定成员是否已经存在于与Key相关联的Set集合中 | ``1``表示已经存在，``0``表示不存在，或该Key本身并不存在
``scard``	 | O(1) 	  | ``scard key`` 获取Set中成员的数量 | 返回Set中``成员的数量``，如果该Key并不存在，返回``0``

更详细参考：[Redis学习手册(Set数据类型)](http://www.cnblogs.com/stephen-liu74/archive/2012/03/21/2352512.html)

```bash
127.0.0.1:6379> SADD myset a b c
(integer) 3
127.0.0.1:6379> SMEMBERS myset
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> SISMEMBER myset a
(integer) 1
127.0.0.1:6379> SISMEMBER myset 1
(integer) 0
127.0.0.1:6379> 
127.0.0.1:6379> SCARD myset
(integer) 3
```

## Redis 有序集合(Redis-Sorted-Sets) ##

应用范围：
> 1). 可以用于一个大型在线游戏的``积分排行榜``。每当玩家的分数发生变化时，可以执行``ZADD``命令``更新``玩家的分数，此后再通过``ZRANGE``命令获取积分``TOP TEN``的用户信息。当然我们也可以利用``ZRANK``命令通过``username``来获取玩家的排行信息。最后我们将组合使用``ZRANGE``和``ZRANK``命令快速的获取和某个玩家``积分相近``的其他用户的信息。
2). Sorted-Sets类型还可用于构建索引数据。

常用方法 | 时间复杂度 | 解释 | 返回值
---		 | ---		  | ---	 | ---
``zadd``	 | O(log(N))  | ``zadd key score member [score member...]`` 时间复杂度中的``N``表示Sorted-Sets中``成员的数量``。添加参数中指定的所有成员及其分数到``指定key``的Sorted-Set中，在该命令中我们可以指定多组score/member作为参数。如果在添加时参数中的某一成员已经存在，该命令将``更新``此成员的分数为新值，同时再将该成员基于新值``重新排序``。如果键``不存在``，该命令将为该键创建一个新的Sorted-Sets Value，并将``score/member``对插入其中。如果该键已经存在，但是与其关联的Value不是Sorted-Sets类型，相关的错误信息将被返回 | 本次操作实际插入的``成员数量``
``zcard``	 | O(1) 	  | ``zcard key`` 获取与该Key相关联的Sorted-Sets中包含的成员数量 | 返回Sorted-Sets中的``成员数量``，如果该Key不存在，返回``0``
``zrange``   | O(log(N)+M)| ``zrange key start stop [WITHSCORES]`` 时间复杂度中的``N``表示Sorted-Set中成员的数量，``M``则表示返回的成员数量。该命令返回顺序在参数``start``和``stop``指定范围内的成员，这里start和stop参数都是0-based，即``0``表示``第一个``成员，``-1``表示``最后一个``成员。如果start大于该Sorted-Set中的最大索引值，或start > stop，此时一个空集合将被返回。如果``stop``大于最大索引值，该命令将返回从``start``到集合的最后一个成员。| 返回索引在start和stop之间的成员列表。
``zrevrange``| O(log(N)+M)| ``zrevrange key start stop [WITHSCORES]`` 时间复杂度中的``N``表示Sorted-Set中成员的数量，``M``则表示返回的成员数量。该命令的功能和ZRANGE基本相同，唯一的差别在于该命令是通过``反向排序``获取指定位置的成员，即``从高到低的顺序``。如果成员具有``相同的分数``，则按``降序字典顺序排序`` | 返回指定的成员列表

更详细参考：[Redis学习手册(Sorted-Sets数据类型)](http://www.cnblogs.com/stephen-liu74/archive/2012/03/23/2354994.html)

```bash
127.0.0.1:6379> ZADD sortset 23 peter
(integer) 1
127.0.0.1:6379> ZADD sortset 33 gwen
(integer) 1
127.0.0.1:6379> ZRANGE sortset 0 -1
1) "peter"
2) "gwen"
127.0.0.1:6379> ZREVRANGE sortset 0 -1
1) "gwen"
2) "peter"
127.0.0.1:6379> ZRANGE sortset 0 -1 withscores
1) "peter"
2) "23"
3) "gwen"
4) "33"
127.0.0.1:6379> ZREVRANGE sortset 0 -1 withscores
1) "gwen"
2) "33"
3) "peter"
4) "23"
127.0.0.1:6379> ZCARD sortset
(integer) 2
```