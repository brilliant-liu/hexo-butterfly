---
title: 一文看懂redis
date: 2020-05-01 09:12:45
tags:
  - redis
  - 教程
categories:
  - [教程,redis]
cover: 'https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2815653840,2206927630&fm=26&gp=0.jpg'
---

# 一文看懂redis

## 什么是redis
Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。它通常被称为数据结构服务器
1. Redis的特点：
    - 数据之间没有必然的关联关系
    - 内部采用单线程机制进行工作
    - 高性能，读写速度快
    - 支持多种数据类型

2. Redis的数据类型：
    + 字符串类型 String
    + 列表类型 list
    + 散列类型 hash
    + 集合类型 set
    + 有序集合 sorted_set

3. Redis的常用使用场景
    * 热点数据的处理
    * 消息队列或者任务队列的使用（例如：秒杀，抢购，购票排队）
    * 实时数据的查询及控制
    * 分布式数据共享（例如分布式集群中的session的分离处理）
    * 分布式锁
        
## redis的基本操作指令
具体的安装操作请自行参考官方文档或者百度安装，本文主要关注命令配置使用等。

### 命令行模式命令详解  
+ 添加数据  
 > set key value
 
+ 查询数据  
 > get key  

示例：
```text
127.0.0.1:6379> set name brilliant-liu
OK
127.0.0.1:6379> get name
"brilliant-liu"
127.0.0.1:6379>
```
+ 清屏指令  
功能描述：清楚屏幕中的信息
> clear

+ 帮助指令  
功能描述：获取命令帮助文档，获取组中所有命令信息名称  
> help 命令名称  
> help @组名  

示例：
```text
127.0.0.1:6379> help
redis-cli 5.0.7
To get help about Redis commands type:
      "help @<group>" to get a list of commands in <group>
      "help <command>" for help on <command>
      "help <tab>" to get a list of possible help topics
      "quit" to exit

To set redis-cli preferences:
      ":set hints" enable online hints
      ":set nohints" disable online hints
Set your preferences in ~/.redisclirc

// help 命令名称
127.0.0.1:6379> help set

  SET key value [expiration EX seconds|PX milliseconds] [NX|XX]  （命令使用格式）
  summary: Set the string value of a key （功能描述）
  since: 1.0.0 （开始支持的版本号）
  group: string   （命令所属群组）

// help @群组
127.0.0.1:6379> help @string

  APPEND key value  （命令使用格式）
  summary: Append a value to a key  （功能描述）
  since: 2.0.0  （开始支持的版本号）

  BITCOUNT key [start end]
  summary: Count set bits in a string
  since: 2.6.0
```
+ 退出  
功能描述：退出客户端  
> quit  
> exit  

### 数据类型的使用
#### string的基本操作
+ 存储格式：redis自身是一个map,其中数据采用key::value的形式存储，一个存储空间保存一个数据，只能存储单个数据，也是最简单的数据存储类型
+ 存储内容：通常只能使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用，但是其仍然是字符串。

string的基本的操作指令
- 添加/修改数据
> set key value

- 获取数据  
> get key

- 删除数据
> del key

- 添加/修改多个数据
> mget key1 value key2 value2 ...

- 获取多个数据
> mget key1 key2 ...  

- 获取数据字符个数（获取字符串长度）
> strlen key

- 追加信息到原始信息尾部，若原始数据不存在则新建
> append key value

- 设置数值数据增加自定范围的值（increment可以为负，为减法操作）
> incr key  
> incrby key increment  
> incrbyfloat key increment  （可以操作小数）  

- 设置数值数据减少指定范围的值
> decr key  
> decrby key increment  

- 设置数据具有指定的生命周期
> setex key seconds value  
> psetex key milliseconds value  

注意事项：
1. string类型作为数值操作时，其内部存储默认为字符串，在进行增减incy/decr等操作时，会转化为整型进行计算。
2. redis的所有操作都是原子性的，因为内部采用单线程处理所有业务，不会有并发问题。
3. string转数值时，要考虑原始数据是否能转，以及是否会超越数值上限，否则报错。数值计算的最大范围（9223372036854775807）和java中的long的一样。
4. 数据为获取到为(nil) 等同于null
5. 单条数据最大的存储量为512MB

部分命令使用示例：   
```text
127.0.0.1:6379> mset age 24 weight 62
OK
127.0.0.1:6379> get age
"24"
127.0.0.1:6379> incr age
(integer) 25
127.0.0.1:6379> incrby age 20
(integer) 45
127.0.0.1:6379> decrby age 15
(integer) 30
127.0.0.1:6379> incrbyfloat age 2.1
"32.1"
127.0.0.1:6379>
```

#### hash的基本操作
+ 存储格式结构：一个存储空间保存多个键值对数据，其底层使用hash表结构实现存储
+ hash存储结构优化：如果字段数量较少，存储结构为类数组结构，如果字段数量较多，存储结构使用hashMap结构。

hash的基本的操作指令
- 添加/修改数据
> hset key field value  

- 获取数据  
> hget key field  
> hgetall key  
 
- 删除数据
> hdel key field1 \[field2]

- 添加/修改多个数据
> hmset key field1 value1 field2 value2 ...

- 获取多个数据
> hmget key field1 field2 ...

- 获取哈希表中字段的数量(获取某一个key中的字段数量)
> hlen key

- 获取哈希表中是否存在指定的字段
> hexists key field

- 获取哈希表中所有字段的名或者字段值
> hkeys key  
> hvals key  

- 设置指定字段的数值数据增加指定范围的值
> hincrby key field increment  
> hincrbyfloat key field increment  

注意事项：  
1.hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在套嵌现象
2.每个hash可以存储2<sup>32</sup>-1个键值对。
3.hgetall可以获取全部属性，如果内部feild过多，遍历数据效率会很低，有可能成为数据方位瓶颈，建议用什么取什么。

部分使用示例：  
```text
127.0.0.1:6379> hset boy age 20
(integer) 1
127.0.0.1:6379> hset boy name brilliant
(integer) 1
127.0.0.1:6379> hget boy age
"20"
127.0.0.1:6379> hgetall boy
1) "age"
2) "20"
3) "name"
4) "brilliant"
127.0.0.1:6379> hkeys boy
1) "age"
2) "name"
127.0.0.1:6379> hincrby boy age 20
(integer) 40
```
#### list类型的基本操作
+ 存储格式结构：一个存储空间保存多个数据，且存在一定的进入顺序。
+ list类型保存多个数据，底层使用双向链表存储结构实现。


list的基本的操作指令
- 添加/修改数据
> lpush key value1 \[value2] ... （从头部添加数据）  
> rpush key value1 \[value2] ... （从尾部添加数据）  

- 查询数据  
> lrange key start stop (stop为-1时，查询start位置到最后一个)  
> lindex key index （获取指定索引位置的值，索引从0开始）  
> llen key   (获取list的长度)  

- 查询并删除数据
> lpop key （从头部查询并删除数据）  
> rpop key （从尾部查询并删除数据）  

- 规定时间内获取并移除数据（等待式获取值并移除）
> blpop key1 \[key2] timeout (在规定的时间内获取list头部第一个值，如果有直接返回，否正等待规定的时间，还没有值返回nil)  
> brpop key1 \[key2] timeout (在规定的时间内获取list尾部第一个值，如果有直接返回，否正等待规定的时间，还没有值返回nil)  

- 移除指定数据
> lrem key count value  (从头部开始删除指定个数count的value值)


注意事项：
1. list的左边添加的数据在前面，右边进入的数据在后面。
2. list中保存的数据都是string类型，数据总量为2<sup>32</sup>-1个元素
3. 获取全部操作的介绍索引为-1  

部分使用示例：
```text
127.0.0.1:6379> lpush list a b c
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> rpush o p
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> lrange list 0 -1
1) "c"
2) "b"
3) "a"
4) "o"
5) "p"
127.0.0.1:6379> lpop list
"c"
127.0.0.1:6379> rpop list
"p"
127.0.0.1:6379>

```


#### set类型的基本操作
+ 存储格式结构：能存储大量的数据，与hash存储结构完全相同，仅存储key，不存储值（nil），且值是不能重复的。

set的基本的操作指令
- 添加/修改数据
> sadd key member1 \[member2]

- 查询数据  
> smembers key

- 查询并删除数据
> srem key member1 \[member2] 

- 获取集合数据总量
> scard key

- 判断集合中是否包含指定数据
> sismember key member  

- 随机获取集合中指定数量的数据
srandmember key \[count]

- 随机获取集合中某个数据并将该数据移除集合
> spop key  

- 求两个集合的交、并、差集
> sinter key1 \[key2]  
> sunion key1 \[key2]  
> sdiff key1 \[key2]   (查缉：key1中的元素去除key2中元素)

- 求两个集合的交、并、差集并存储到指定的集合中
> sinterstore destination key1 \[key2]
> sunionstore destination key1 \[key2]
> sdidffstore destination key1 \[key2]

- 将指定数据从原始集合中移动到目标集合中
> smove source destination member

注意事项：
1. set类型不允许数据重复
2. set与hash的存储结构相同，但是无法启用hash中存储的空间
  
部分命令使用示例：
```text
127.0.0.1:6379> smembers set
1) "a"
2) "bb"
3) "c"
127.0.0.1:6379> srem set a
(integer) 1
127.0.0.1:6379> smembers set
1) "bb"
2) "c"
127.0.0.1:6379> scard set
(integer) 2
127.0.0.1:6379> sismember set c
(integer) 1
127.0.0.1:6379> srandmember set 2
1) "bb"
2) "c"
127.0.0.1:6379> spop set
"bb"
127.0.0.1:6379> smembers set
1) "c"
```

#### sorted_set类型的基本操作
+ 存储格式结构：在set的存储结构的基础上增加一个可排序的字段score。

set的基本的操作指令
- 添加数据
> zadd key score1 member1 \[score2 member2]  

- 查询全部数据  
> zrange key start stop \[WITHCORES]     （升序排序）  
> zrevrange key start stop \[WITHCORES]    （逆向排序）  

- 删除数据
> zrem key member1 \[member2 ...]  

- 按条件获取数据
> zrangebyscore key min max \[WITHSCORES] \[LIMIT]  
> zrevrangebyscore key max min \[WITHSCORES]  

- 条件删除
> zremrangebyrank key start stop  
> zremrangebyscore key min max  

- 获取集合数据总量
> zcard key  
> zcount key min max  

- 集合交、并操作，将操作结果存储到destination中
> zinterstore destination numkeys key \[key ...] (numkeys为要合并的set数量)  
> zunionstore destination numkeys key \[key ...]

- 获取数据对应的索引
> zrank rey member   （从小到大排序）
> zrevrank key member  （从大到小排序）

- score值获取与修改
> zscore key member  
> zincrby key increment member   (增加操作)

注意事项：
+ min与max用于限制查询条件
+ start与stop用于查询范围，作用与索引，从0开始，-1标识全部
+ offset和count用于限定查询范围，标识开始位置和数据总量
+ score保存的数据存储空间时64位的，如果是整数，注意保存范围。
+ score也可以保存双精度的double值，可能会丢失精度。

部分命令使用示例：  
```text
127.0.0.1:6379> zadd sort 12 aa 23 bb 35 c 12 dd
(integer) 4
127.0.0.1:6379> zrange sort 0 -1
1) "aa"
2) "dd"
3) "bb"
4) "c"
127.0.0.1:6379> zrange sort 0 -1 WITHSCORES
1) "aa"
2) "12"
3) "dd"
4) "12"
5) "bb"
6) "23"
7) "c"
8) "35"
127.0.0.1:6379> zrangebyscore sort 12 23 WITHSCORES
1) "aa"
2) "12"
3) "dd"
4) "12"
5) "bb"
6) "23"
127.0.0.1:6379> zrangebyscore sort 12 23 WITHSCORES limit 0 2
1) "aa"
2) "12"
3) "dd"
4) "12"
127.0.0.1:6379> zadd z1 12 ss 23 bb
(integer) 2
127.0.0.1:6379> zadd z2 58 ggg
(integer) 1
127.0.0.1:6379> zunionstore z3 2 z1 z2
(integer) 3
127.0.0.1:6379> zrange z3 0 -1
1) "ss"
2) "bb"
3) "ggg"

```

## redis的通用指令
### key的通用操作
- 删除指定的key
> del key  

- 获取key是否存在
> exists key  

- 获取key的类型
> type key

- 为key设置有效期
> expire key seconds  （使用秒作为有效期单位）  
> pexpire key milliseconds  （使用毫秒作为有效期单位）  
> expire key timestamp  （使用时间戳作为有效期单位）  
> pexpire key millisseconds-timestamp   

- 获取key的有效期
> ttl key   (返回值：-2：表示key不存在；-1：表示key存在；>0:表示有效时长，单位秒)  
> pttl key  (单位毫秒)  

- 切换key从时效性转化为永久性
> persist key

- 查询key （详见查询模式规则）
> keys pattern

- key改名
> rename key newkey  (如果newkey存在时，会覆盖newkey的数据)  
> renamenx key newkey  (如果newkey存在时，命名不会成功，不会覆盖newkey的数据)  

- 对所有的key排序
> sort

- 其他key的通用操作
> help @generic

查询模式规则
+ *：匹配任意数量的符号  
+ ?：匹配任意一个符号  
+ \[ ]：匹配一个指定的符号  

部分命令使用示例：  
```text
127.0.0.1:6379> keys *
 1) "name"
 2) "weight"
 3) "o"
 4) "sort"
 5) "list"
 6) "set1"
 7) "z1"
 8) "boy"
 9) "z3"
10) "z2"
127.0.0.1:6379> keys *e*
1) "name"
2) "weight"
3) "set1"
127.0.0.1:6379> keys z[23]
1) "z3"
2) "z2"
127.0.0.1:6379> keys s??t
1) "sort"
127.0.0.1:6379> lrange aa 0 -1
1) "112"
2) "345"
3) "234"
4) "123"
127.0.0.1:6379> sort aa desc
1) "345"
2) "234"
3) "123"
4) "112"
127.0.0.1:6379> sort aa asc
1) "112"
2) "123"
3) "234"
4) "345"
```
注意事项：
1.key的重复问题，redis为每个服务提供16个数据库，编号从0-15，且每个数据库独立互不影响。

### 数据库db的基本操作
- 切换数据库
> select index （编号从0-15，默认为0）

- 数据移动
> move key db

- 数据清除
> dbsize (查看key的数量)  
> flushdb (删除当前数据库数据)  
> flushall (删除所有数据库数据)  

- 其他操作
> quit （退出）  
> ping (测试服务器联通性)  
> echo message （类似输出内容到控制台日志）  

部分命令使用示例：  
```text

127.0.0.1:6379> keys *
 1) "weight"
 2) "o"
 3) "sort"
 4) "list"
 5) "set1"
 6) "z1"
 7) "aa"
 8) "boy"
 9) "names"
10) "z3"
11) "z2"
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> move list 1
(integer) 1
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "list"

```
## redis的高级操作
### redis持久化
    利用永久性存储介质将内存中的数据进行保存，在特定的事件将保存的数据进行恢复的工作机制称为持久化
### 持久化的保存方案
1. 快照形式（RDB）：把当前的数据状态进行保存，快照形式，存储数据结果，存储简单，重点关注数据内容。
2. 过程日志(AOF)：把对数据的操作过程日志记录下来，在恢复数据时，根据操作过程进行恢复，存储格式复杂，重点关注数据的操作过程。

### RDB启动方式

RDB启动方式，持久化命令  
> save  
> bgsave

RDB启动方式，相关配置   
- 设置本地数据库的文件名，默认为dump.rdb,建议设置为 dump.端口号.rdb  
    > dbfilename dump.rdb
- 设置存储rdb文件的路径    
    > dir
- 设置存储到本地数据库是否压缩数据，默认为yes,采用LZF压缩。  
    > rdbcompression yes

- 设置是否进行RDB文件格式的校验，该校验过程在写文件和读文件的过程均执行，默认开启。
    > rdbchecksum yes

- bgsave指令配置：后台存储过程如果出现错误，是否停止保存操作，建议默认开启。 
    > stop-writes-on-bgsave-error yes

- save配置限定时间内key的变化数量达到指定数量，即进行持久化(second为秒数，change为监控key的变化数量阈值)
    > save second changes
- 服务器允许过程中重启
    > debug reload

- 关闭服务器保存数据
    > shutdown save

RDB启动方式的对比

|方式|save指令|bgsave指令|
|:----:|:----:|:----:|
|读写|同步|异步|
|阻塞客户端指令|是|否|
|额外内存消耗|否|是|
|启动新进程|否|是|

RDB的优缺点  
+ 优点：
1. RDB是一个压缩紧凑的二进制文件，存储效率高
2. RDB保存的是redis在某个时间点的数据快照，适用于数据备份，全量复制等场景，且其恢复数据比AOF要快。

+ 缺点：
1. 无法做到实时持久化，只能保存redis在某个时间点的数据快照，可能会丢失数据
2. bgsave每次执行需要调用fork函数操作子进程，会牺牲一部分性能。
3. 各个版本的redis保存的rdb文件不兼容，可能无法解析。

注意事项：  
+ save指令:因为redis是单线程任务执行序列，save指令的执行,会立即执行数据保存，其会阻塞当前Redis服务器，直到RDB过程执行完成为止，有可能会造成长时间的阻塞，线上尽量不要使用。
+ bgsave指令：bgsave执行指令发送之后，redis会返回background saving started ,同时会调用fork函数，生成子进程进行保存操作。
+ save配置，默认使用bgsave的形式，进行数据的保存。


### AOF启动方式
AOF写数据的三种策略（appendfsync）  
+ always (每次写入操作都同步到AOF文件中，性能较低)
+ everysec (每秒都将操作过程同步到AOF文件中)
+ no (系统控制，整个过程不可控)

AOF的相关配置  
+ 启动配置
    > appendonly yes|no
+ 是否开启AOF持久化功能(AOF写数据的三种策略)，默认不开启
    > appendfsync always|everysec|no

AOF的重写  
    所谓的AOF重写，就是对同一个数据的若干条命令执行结果转化为最终结果数据对应的指令进行合并记录到appendonly.aof持久化文件中。
    
1. AOF的重写规则：
+ 进程内已经超时的数据不在写入
+ 忽略无效指令，如删除指令，del key,hdel key2 ...
+ 对同一个数据的多条命令合并为一条命令。

2. AOF的重写方式
+ 手动重写
    > bgrewriteaof  
+ 自动重写
    > auto-aof-rewrite-min-size size  
    
    > auto-aof-rewrite-percentage  percent (自动重写的百分比，达到该值即重写)

+ 重写对比条件和触发条件
```text
1. 自动重写触发对比参数（运行指令info Persistence获取具体信息）
aof_current_size 、aof_base_size  
2. 自动触发条件
auto-aof-rewrite-min-size配置触发条件： aof_current_size > auto-aof-rewrite-min-size  
auto-aof-rewrite-percentage配置的触发条件：(aof_current_size - aof_base_size) / aof_base_size >= auto-aof-rewrite-percentage
```
+ 重写流程图：  
![image.png](https://i.loli.net/2020/05/16/XFtBHMWhnImvuJc.png)


AOF和RDB的对比  

|持久化方式|RDB|AOF|
|:----:|:----:|:----:|
|占用空间|小|大|
|存储速度|慢|快|
|恢复速度|快|慢|
|数据安全性|会丢失数据|根据策略决定|
|资源消耗|高/重量级|低/轻量级|
|启动优先级|低|高|

### redis事务
#### 事务的基本操作
+ 开启事务 (设定事务的开启位置)
    > multi
+ 执行事务（exec需要和multi成对使用）
    > exec
+ 取消事务
    > discard

注意事项：
1. 如果命令格式书写错误，所有事务均不执行
2. 如果命令格式正确，但是无法正确执行，那么正确的会执行，运行错误的不会执行。且已经执行完的语句，不会回滚。

#### 锁 
1. 普通锁
+ 对key添加监控锁，在执行exec前如果key发生了变化，终止事务操作。
    > watch key1 \[key2...]
+ 取消对所有key的监视
    > unwatch

2. 分布式锁
+ 使用setnx设置一个公共锁 
    > setnx lock-key value
+ 删除锁
    > del lock-key

3. 分布式锁的改良
使用expire为锁田间时间限定，到时不释放，系统会自动释放
    > expire lock-key second  
                                             
    > pexpire lock-key millisconds  

注意事项：
1. 事务只能在watch之内定义。
2.setnx锁，要约定好，大家都同时进行这个锁的控制，否正是不会生效的，使用完后，要及时删除。设置锁时有值则返回设置失败，不具有控制权，只能等待；无值返回设置成功，拥有控制权，可以进行具体的处理


###  redis删除策略
#### redis删除策略类型
+ 定时删除
    > 创建一个定时器，当key设置有过期时间，且过期时间达到时，由定时任务执行对键的删除操作，比较节约内存，能快速释放不必要的内存占用。  
+ 惰性删除
    > 数据到达过期时间，不做处理，等下次访问的时候在删除。并返回不存在。  节约cup性能，但会长期占用内存。
+ 定期删除 
    > 在redis启动的时候，，会读取server.hz的值，默认为0，然后每秒钟执行server.hz中的serverCron().databasesCron().activeExpireCycle(),
     并对expire\[*]逐一检查，且每次执行时长为250ms/server.hz,在检查时会随机挑选出W个key检查，过期则删除。W取值等于ACTIVE_EXPIRE_CYCLE_LOOKUP_PER_LOOP的属性值。


|删除策略|内存|cpu|特点|
|:----:|:----|:----|:----|
|定时删除|节约内存，无占用|cpu占用高|拿时间换空间|
|惰性删除|内存占用较高|延迟执行，cpu利用率高|拿空间换时间|
|定期删除|内存定期随机清理|花费固定的cpu时间维护内存|随机抽查，重点抽查|

#### 逐出算法（淘汰算法）
1. 相关配置
+ 最大可使用内存 (默认为0，表示不限制，一般设置为环境内存的50%)
    > maxmemory
+ 每次选取待删除数据的个数（采用随机获取数据的方式作为待检测删除数据）
    > maxmemory-samples
+ 删除策略 (达到最大内存后，对挑选出来的数据进行删除的策略)
    > maxmemory-policy

2. 删除策略（maxmemory-policy）的配置项
2.1 逐出的数据都是检测易失数据，即存在过期时间的数据 (可能会过期的数据集`server.db[*].expires`） 
+ volatile-lru：挑选出最近最少使用的数据淘汰 （least recently used）  
+ volatile-lfu: 挑选出最近使用次数最少的数据淘汰 (least frequently used)  
+ volatile-ttl: 挑选将要过期的数据淘汰  
+ volatile-random 任意选择数据淘汰   
    
2.2 检测全库数据（会删除不存在过期时间的数据）  
+ allkeys-lru: 挑选出最近最少使用的数据淘汰 （least recently used）  
+ allkeys-lfu: 挑选出最近使用次数最少的数据淘汰 (least frequently used)  
+ allkeys-random: 任意数据淘汰  
    
2.3 放弃数据驱逐  
+ no-enviction: 禁止删除数据（redis4.0默认策略。会引发OOM(out of memory)）  

> PS：通过INFO命令输出监控信息，查询缓存hit和miss的次数，根据业务需求优化redis配置

### 服务器端配置
+ 设置服务器以守护进程的方式运行
    > daemonize yes|on
+ 绑定主机地址(一旦绑定，只能通过该地址访问redis)
    > bing 127.0.0.1
+ 设置服务器端口
    > port 6379
+ 设置数据库数量
    > databases 16
+ 设置服务器以指定日志记录级别 （默认verbose）
    > loglevel debug|verbose|notice|warning
+ 日志记录文件名
    > logfile 端口号.log       
+ 设置同一时间客户端最大连接数，默认无限制，若达到上限，redis会关闭新的连接
    > maxclients 0
+ 客户端闲置等待最大时长，达到后关闭连接，单位秒。如需关闭，设置为0
    > timeout 300
+ 导入并加载指定配置文件信息，用于快速创建redis公共配置较多的redis实例配置文件，便于维护. 
    > include /path/server-端口号.conf
 

### redis的高级数据类型
- Bitmaps
- HyperLogLog
- GEO

1. Bitmaps
存储原理：
对于一些简单的数据，只需要保存状态，例如0：否，1：是。而且计算机中最小的存储单位为位，一个字节有8位，那么每一位都可以表示一种状态。

+ 获取指定key对于偏移量上的bit值 (默认为0)
    > getbit key offset  
+ 设置指定key对于偏移量上的bit值，value只能位1或者0
    > setbit key offset value  
+ 对指定key按位进行交and、并or、非not、异或xor操作，并将结果保存到destkey中
    > bitmap \[and\or\not\xor] key1 \[key2...]  
+ 统计指定key中1的数量
    > bitcount key \[start end]  

2. HyperLogLog
- 基数：基数是数据集去重后的元素个数
- HyperLogLog是赢基数统计的，运用了LogLog的算法。

例如：
{1，3，5，7，5，7}，基数集：{1，3，5，7}，基数：5  
{1，1，1，1，7}，基数集：{1，7}，基数：2   

HyperLogLog基本操作
+ 添加数据
    > pfadd key element \[element ...]
+ 统计数据
    > pfcount key \[key ...]
+ 合并数据
    > pfmerge destkey sourcekey \[sourcekey...]

注意事项：
1. HyperLogLog使用场景，可以用于独立信息的统计  
2. 其用于基数统计，不是集合，不保存数据，只记录数据数量，而不是具体的数据  
3. 其核心数基数估算算法，最终数值存在一定误差，数据量达到一定量的时候，结果误差约0.81%
4. 其耗空间极小，每个HyperLogLog key的占用最大都为12K

使用示例：  
```text
127.0.0.1:6379> pfadd hll 001 001 001 001 002
(integer) 1
127.0.0.1:6379> pfcount hll
(integer) 2
127.0.0.1:6379> pfadd fff 002 03 26
(integer) 1
127.0.0.1:6379> pfmerge newkey  hll fff
OK
127.0.0.1:6379> pfcount newkey
(integer) 4
127.0.0.1:6379>
```

3. GEO
+ 添加坐标点
    > geoadd key longitude latitude member \[longitude latitude member ...]
+ 获取坐标点
    > geopos key member \[member ...]
+ 计算坐标点
    > geodist key member1 member2 \[unit]  
+ 根据坐标求范围内的数据
georadius key longitude latitude radius m|km|ft|mi 
+ 根据求点范围内的数据
georadiusbymember key member radius m|km|ft|mi 
+ 获取指定点对应坐标hash值
geohash key member \[member ...]

注意事项：
1. 只计算水平位置的计算。 


### redis集群
#### 主从复制
互联网的“三高”架构：高并发、高性能、高可用  
单机redis的风险与问题： 1. 机器故障   2. 容量瓶颈    

主从复制：将master中的数据即时的，有效的复制到slave中  
特征：一个master对应多个slave，一个slave只能对应一个master  
master: 写数据，执行写操作时，将出现变化的数据自动同步到slave，读数据（可忽略）
slave: 读数据，禁止写数据。

主从复制的作用：
1. 读写分离：master写数据，slave读取数据，提高服务器的读写负载能力
2. 负载均衡：基于主从结构，配合读写分离，由slave分担master负载，可以根据需求变化，改变slave的数量，从而分担多个从节点数据读取的压力。
3. 故障恢复，master出现故障时，可由slave中推选出新的master,实现快速故障恢复
4. 数据冗余：实现热点数据热备份，时持久化的一种数据冗余方式。
5. 是高可用的基石，搭配哨兵模式，可以搭建高可用集群。

master和slave建立连接的过程：

![image.png](https://i.loli.net/2020/05/16/5SGh2MIJ16zHkWZ.png)  
 
1. 主从连接的方式   
+ 方式一：客户端发送命令
    > slaveof <masterip> <masterport>
+ 方式二：启动服务器参数
    > redis-server -slaveof <masterip> <masterport>
+ 方式三：服务器配置
    > slaveof <masterip> <masterport>

2. 主从的断开方式  
+ 客户端发送命令  
    > slaveof no one

3. 授权访问
+ master配置文件设置密码：
    > requirepass <password>
+ master客户端发送命令设置密码
    > config set requirepass <password>
    
    > config get requirepass
+ slave客户端发送命令设置密码
    > auth <password>
+ slave配置文件设置密码
    > masterauth <password>
+ 启动客户端设置密码
    > redis-cli -a <password>

4. 数据同步的流程：
注意两个同步数据的地方，一个全量复制，一个主服务器的复制积压缓冲区的数据部分复制。  

![image.png](https://i.loli.net/2020/05/16/CSj4VEZPhcp8HeG.png)

![image.png](https://i.loli.net/2020/05/16/2pLWqtHgv1V4h87.png)  

部分复制的三个核心要素：  
- 服务器的运行id(run id)  （该ID是服务器每次运行的身份识别码，是一个随机的40位16进制字符）
- 主服务器的复制积压缓冲区  （其是一个FIFO的队列，用于存储服务器执行过的命令，每次传播，master会将命令记录下来，存储在复制缓冲区）
- 主服务的复制偏移量  （复制缓冲区中会对存储的数据进行偏移量offset的编码，不同的slave在数据同步时，会根据offset进行区分传播的差异）

注意事项：
1. 如果master数据量较大，同步时应尽量避开流量高峰，避免造成master阻塞。  
2. 复制缓冲区的大小舍弟那个不合理，会导致数据溢出。复制缓冲区的配置项为：`repl-backlog-size 1mb`   
3. master单机内存占用主机内存比例不应该过大，建议使用50%-70%的内存，剩余的内存用于bgsave命令和创建复制缓存区。  
4. 为了避免slave进行全量复制，部分复制服务器相应阻塞或者数据不同步，建议关闭此期间的对外服务。 配置指令：`slave-serve-stale-date yes|no`  
5. 数据同步阶段，如果多个slave向master请求数据同步，master发送的RDB文件增多，会占用带宽，建议根据业务错峰安排数据同步。


5. 心跳机制 
进入命令传播阶段，master和slave之间需要进行信息交换，使用心跳机制进行维护，实现双方保持在线。  
master心跳：通过ping , 周期默认位10秒，可通过配置：`repl-ping-slave-period`  
slave心跳：通过replconf ack {offset} ,周期位1s,汇报自己的复制偏移量，获取最新数据变更指令，并判断master是否在线。

心跳的注意事项：
1. 当slave多数掉线时，或者延迟过高时，master为了保障稳定性，将拒绝所有信息的同步，配置如下(slave数量少于2时或者所有slave延迟都大于等于8秒，强制关闭master写同步)
    > min-slaves-to-write 2 

    > min-slaves-max-lag 8


6.主从复制的常见问题：
1. 频繁的全量复制：  
+ master数据量会越来越大，一旦master重启，run id将变化，会导致slaved的全量复制操作。
    内部优化方案：  
    1. master内部创建master_replid变量，使用相同的run id相同策略生成，并发给slave
    2. master关闭执行命令shutdown save,进行RDB持久化，将run id和offset保存到 RDB文件中。通过 `redis-check-rdb rdb文件名 `命令查看。
+ 网络环境不好，出现网络中断，slave不提供服务，导致slave反复进行全量复制。  
优化方案：
修改复制缓冲区的大小 `repl-backlog-size`。  

2. 频繁的网络中断：  
导致master占用cpu较高或者slave频繁断开  
解决方法：
    1. 可以尝试使用   `repl-timeout` 配置适当设置超时连接时间，默认60秒。
    2. 提高master的ping指令的发送频度，通过配置`repl-ping-slave-period`实现，一般`repl-time`的时间最好为ping频度的5-10倍.

3. 多个slave中的数据不一致
解决办法：
    1. 优化网络环境，最好部署在一个机房
    2. 监控主节点的网络延迟，如果延迟过高，可以通过`slave-serve-stale-data yes|no`暂时屏蔽程序对slave的数据访问。


#### 哨兵模式
哨兵：是一个分布式系统，用于对主从结构中的每台服务器进行监控，每当出现故障，通过投票的机制选择新的master并将所有的slave连接到新的master。  
哨兵的作用：
1.监控：不断检查master和slave是否正常运行  
2.通知：当监控服务器出现问题，向其他哨兵、客户端发送通知  
3.自动故障转移：断开master与slave的连接，并选取一个slave作为master,将其他的slave连接到新的master,并告诉客户端新的服务器地址。  

哨兵的配置： sentinel.conf文件
+ 启动哨兵：
    > redis-sentinel sentinel-端口号.conf


注意事项：哨兵也是一台redis服务器，只是不提供服务。通知器配置的数量为单数。


#### 集群

数据存储的设计：
通过算法设计，计算出key应该保存的位置，将所有的存储空间计划分割16384份，每台主机保存一部分，每份代表一个存储空间成为槽，不是key的保存空间。根据槽中编号，能获取到key所在的主机位置。

将key按照计算出的结果，放到对应的存储空间。

集群部分配置（redis.conf文件配置）
+ 设置加入cluster,成为其中的节点
    > cluster-enabled yes
+ cluster配置文件名，该文件自动生成，仅用于快速查找文件并查询文件内容
    > cluster-config-file nodes-端口号.conf
+ 节点超时下线时间，用于判断该节点是否下线或者切换为从节点
    > cluster-node-timeout
+ master连接的slave最小数量
    > cluster-migration-barrier <count>
+ 查看集群节点信息
    > cluster nodes
+ 进入一个从节点redis,切换为主节点
    > cluster replicate <master-id>
+ 发现一个新节点，新增主节点
    > cluster meet ip:port
+ 忽略一个没有solt的节点
    > cluster forget <id>
+ 手动故障转移
    > cluster failover

### 缓存预热
缓存预热：服务器启动后迅速宕机，因为请求数据量交高，主从直接数据吞吐量较大，数据同步操作频度交高。  
问题解决：  
1. 可以使用脚本程序固定触发数据预热过程  
2. 如果条件允许，使用CDN(内容分发网络)，效果会更好。  

### 缓存雪崩
瞬间过期数据量太大，导致数据服务器压力过大从而引起一系列数据库服务器、redis集群崩溃的问题。  
解决方案：  
1. 将过期策略LRU与LFU的切换
2. 数据有效期的调整
3. 构建多级缓存架构，nginx缓存+redis缓存+ehcache缓存
4. 优化Mysql耗时
5. 进行灾难预警监控。
6. 限流，降级

### 缓存击穿 
redis中某个key过期，该key的访问量巨大，多个数据请求redis后，均为命中，造成短时间内发起对数据库的同一数据的请求。
解决方案：
1. 调整key的过期时间   
2. 设置二级缓存，保障不会同时被淘汰。  
3. 加锁，使用分布式锁，防止被击穿。

### 缓存穿透
系统平稳运行中，redis服务器命中率随时间逐步降低，redis内存平稳，内存无压力，但是cup占用激增，数据库服务器压力激增，导致数据库崩溃。  
可能导致的原因：redis中大面积出现未命中，出现非正常访问URL。例如黑客攻击。  
解决方案：  
1. 白名单策略
2. 实施监控，对访问进行黑名单防空。
3. key加密，如果key不满足规则，驳回数据请求访问。  

> 本文主要是在学习redis的笔记，便于以后复习，写的不是很好。希望给自己带来好处的同时也能给各位小白们部分启发。
在此，特别感谢视频老师的讲解。并附学习视频地址： http://yun.itheima.com/course/611.html?stt