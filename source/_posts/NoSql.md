---
title: NoSql
date: 2021-12-06 16:40:41
tags:
  - 数据库
categories:
  - [教程]
cover: https://img1.baidu.com/it/u=3959687249,3625365722&fm=26&fmt=auto


---



# NoSql



## NoSql概念

NoSQL，泛指非关系型的数据库。



## NoSql的分类

**键值(Key-Value)存储数据库**

这一类数据库主要会使用到一个[哈希表](https://baike.baidu.com/item/哈希表)，这个表中有一个特定的键和一个指针指向特定的数据。Key/value模型对于IT系统来说的优势在于简单、易部署。但是如果[数据库管理员](https://baike.baidu.com/item/数据库管理员/1216449)([DBA](https://baike.baidu.com/item/DBA/3349))只对部分值进行查询或更新的时候，Key/value就显得效率低下了。举例如：Tokyo Cabinet/Tyrant， Redis， Voldemort， Oracle BDB。

**列存储数据库**

这部分数据库通常是用来应对分布式存储的海量数据。键仍然存在，但是它们的特点是指向了多个列。这些列是由列家族来安排的。如：Cassandra， HBase， Riak.

**文档型数据库**

文档型数据库的灵感是来自于Lotus Notes办公软件的，而且它同第一种键值存储相类似。该类型的数据模型是版本化的文档，半结构化的文档以特定的格式存储，比如JSON。文档型数据库可以看作是键值数据库的升级版，允许之间嵌套键值，在处理网页等复杂数据时，文档型数据库比传统键值数据库的查询效率更高。如：CouchDB， **MongoDb**. 国内也有文档型数据库SequoiaDB，已经开源。

**图形(Graph)数据库**

图形结构的数据库同其他行列以及刚性结构的SQL数据库不同，它是使用灵活的图形模型，并且能够扩展到多个服务器上。NoSQL数据库没有标准的查询语言(SQL)，因此进行数据库查询需要制定数据模型。许多NoSQL数据库都有REST式的数据接口或者查询API。如：Neo4J， InfoGrid， Infinite Graph。



**不同分类特点对比**

| **分类**              | **Examples举例**                                      | 典型应用场景                                                 | 数据模型                                        | 优点                                                         | 缺点                                                         |
| --------------------- | ----------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **键值（key-value）** | Tokyo Cabinet/Tyrant， Redis， Voldemort， Oracle BDB | 内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等。 | Key 指向 Value 的键值对，通常用hash table来实现 | 查找速度快                                                   | 数据无结构化，通常只被当作字符串或者二进制数据               |
| **列存储数据库**      | Cassandra， HBase， Riak                              | 分布式的文件系统                                             | 以列簇式存储，将同一列数据存在一起              | 查找速度快，可扩展性强，更容易进行分布式扩展                 | 功能相对局限                                                 |
| **文档型数据库**      | CouchDB， MongoDb                                     | Web应用（与Key-Value类似，Value是结构化的，不同的是数据库能够了解Value的内容） | Key-Value对应的键值对，Value为结构化数据        | 数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构 | 查询性能不高，而且缺乏统一的查询语法。                       |
| **图形(Graph)数据库** | Neo4J， InfoGrid， Infinite Graph                     | 社交网络，推荐系统等。专注于构建关系图谱                     | 图结构                                          | 利用图结构相关算法。比如最短路径寻址，N度关系查找等          | 很多时候需要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群方案。 |



## NoSql的优点

1、易扩展

NoSQL数据库种类繁多，但是一个共同的特点都是去掉关系数据库的关系型特性。数据之间无关系，这样就非常容易扩展。也无形之间，在架构的层面上带来了可扩展的能力。

2、大数据量，高性能

NoSQL数据库都具有非常高的读写性能，尤其在大数据量下，同样表现优秀。这得益于它的无关系性，数据库的结构简单。一般MySQL使用Query Cache，每次表的更新Cache就失效，是一种大粒度的Cache，在针对web2.0的交互频繁的应用，Cache性能不高。而NoSQL的Cache是记录级的，是一种细粒度的Cache，所以NoSQL在这个层面上来说就要性能高很多了。

3、灵活的数据模型

NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。而在关系数据库里，增删字段是一件非常麻烦的事情。如果是非常大数据量的表，增加字段简直就是一个噩梦。这点在大数据量的web2.0时代尤其明显。

4、高可用

NoSQL在不太影响性能的情况，就可以方便的实现高可用的架构。比如Cassandra，HBase模型，通过复制模型也能实现高可用。



## MongoDB

MongoDB 是由C++语言编写的，是一个**基于分布式文件存储的开源数据库系统**。运行稳定，性能高。

### **特点**

+ 模式自由，可以吧不同结构的文档存储在同一个数据库中。
+ 面向集合的存储，适合存储JSON风格文件的形式。MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

+ 复制和高可用性：支持服务器之间数据的复制，支持主从模式。在高负载的情况下，添加更多的节点，可以保证服务器性能。
+ 自动分片：支持云级别的伸缩性，自动分片功能支持水平数据库集群，可动态添加额外的机器。
+ 丰富的查询，支持丰富的查询表达式。
+ 快速就地更新：查询优化器会分析查询表达式，并生成一个高效的查询计划。
+ 高效的传统存储方式，支持二进制数据，以及大型对象（如照片）



### MongoDB的术语

| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                           |
| :----------- | :--------------- | :---------------------------------- |
| database     | database         | 数据库                              |
| table        | collection       | 数据库表/集合                       |
| row          | document         | 数据记录行/文档                     |
| column       | field            | 数据字段/域                         |
| index        | index            | 索引                                |
| table joins  |                  | 表连接,MongoDB不支持                |
| primary key  | primary key      | 主键,MongoDB自动将_id字段设置为主键 |

三元素：

**数据库，集合、文档**



#### 文档

文档对应着关系型数据库中的行，就是一个对象，由简直对构成，是json的扩展Bson形式。

如 {"site":"www", "name":"自学教程"}



> 特别注意：
>
> 1. 文档中的键/值对是有序的。
> 2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
> 3. MongoDB区分类型和大小写。
> 4. MongoDB的文档不能有重复的键。
> 5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。
> 6. 文档键命名规范：
>    - 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
>    - .和$有特别的意义，只有在特定环境下才能使用。
>    - 以下划线"_"开头的键是保留的(不是严格要求的)。



#### 集合

集合就是 MongoDB 文档组，集合就是关系型数据库中的表概念，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。



比如，我们可以将以下不同数据结构的文档插入到集合中：

```
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
```

当第一个文档插入时，集合就会被创建。



> 注意：
>
> 合法的集合名
>
> - 集合名不能是空字符串""。
> - 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
> - 集合名不能以"system."开头，这是为系统集合保留的前缀。
> - 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。　



#### MongoDB 数据类型

下表为MongoDB中常用的几种数据类型。

| 数据类型           | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                            |
| Double             | 双精度浮点值。用于存储浮点值。                               |
| Min/Max keys       | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Array              | 用于将数组或列表或多个值存储为一个键。                       |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                       |
| Object             | 用于内嵌文档。                                               |
| Null               | 用于创建空值。                                               |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的 ID。                                 |
| Binary Data        | 二进制数据。用于存储二进制数据。                             |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                 |
| Regular expression | 正则表达式类型。用于存储正则表达式。                         |

下面说明下几种重要的数据类型。

ObjectId

ObjectId 类似唯一主键，可以很快的去生成和排序，包含 12 bytes，含义是：

- 前 4 个字节表示创建 **unix** 时间戳,格林尼治时间 **UTC** 时间，比北京时间晚了 8 个小时
- 接下来的 3 个字节是机器标识码
- 紧接的两个字节由进程 id 组成 PID
- 最后三个字节是随机数



**MongoDB 中存储的文档必须有一个 _id 键。**这个键的值可以是任何类型的，默认是个 ObjectId 对象

由于 ObjectId 中保存了创建的时间戳，所以你不需要为你的文档保存时间戳字段，你可以通过 getTimestamp 函数来获取文档的创建时间:

```
> var newObject = ObjectId()
> newObject.getTimestamp()
ISODate("2017-11-25T07:21:10Z")
```

ObjectId 转为字符串

```
> newObject.str
5a1919e63df83ce79df8b38f
```



### 数据库操作



#### 创建数据库

语法格式如下：

```
use DATABASE_NAME
```

如果数据库不存在，则创建数据库，否则切换到指定数据库。



#### 查看所有数据库

可以使用 **show dbs** 命令：



#### 删除数据库

```
db.dropDatabase()
```

删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。



#### 查看集合

```
show collections
```

如果要查看已有集合，可以使用 **show collections** 或 **show tables** 命令：

```
> show collections
runoob
system.indexes
```



#### 创建集合

```
db.createCollection(name, options)
```

参数说明：

- name: 要创建的集合名称
- options: 可选参数, 指定有关内存大小及索引的选项

options 可以是如下参数：

| 字段        | 类型 | 描述                                                         |
| :---------- | :--- | :----------------------------------------------------------- |
| capped      | 布尔 | （可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。 **当该值为 true 时，必须指定 size 参数。** |
| autoIndexId | 布尔 | 3.2 之后不再支持该参数。（可选）如为 true，自动在 _id 字段创建索引。默认为 false。 |
| size        | 数值 | （可选）为固定集合指定一个最大值，即字节数。 **如果 capped 为 true，也需要指定该字段。** |
| max         | 数值 | （可选）指定固定集合中包含文档的最大数量。                   |

在插入文档时，MongoDB 首先检查固定集合的 size 字段，然后检查 max 字段。



> 在 MongoDB 中，你不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合。例如：
>
> ```
> > db.mycol2.insert({"name" : "菜鸟教程"})
> > show collections
> mycol2
> ```



#### 删除集合

```
db.collection.drop()
```



#### 插入文档

MongoDB 使用 insert() 或 save() 方法向集合中插入文档，语法如下：

```
db.COLLECTION_NAME.insert(document)
或
db.COLLECTION_NAME.save(document)
```

- save()：如果 _id 主键存在则更新数据，如果不存在就插入数据。该方法新版本中已废弃，可以使用 **db.collection.insertOne()** 或 **db.collection.replaceOne()** 来代替。
- insert(): 若插入的数据主键已经存在，则会抛 **org.springframework.dao.DuplicateKeyException** 异常，提示主键重复，不保存当前数据。



> 在 MongoDB 中，你不需要创建集合。当你插入一些文档时，MongoDB 会自动创建集合。例如：
>
> ```
> > db.mycol2.insert({"name" : "菜鸟教程"})
> > show collections
> mycol2
> ```



#### 更新文档

update() 方法用于更新已存在的文档。语法格式如下：

```
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

**参数说明：**

- **query** : update的查询条件，类似sql update查询内where后面的。

- **update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的

- **upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。

- **multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

  >  db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}},{mutil:true})

- **writeConcern** :可选，抛出异常的级别。

> 注意：
>
> 指定属性更新，通过操作符`$set`指定
>
> db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})
> WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })   # 输出信息



#### 删除文档

MongoDB remove() 函数是用来移除集合中的数据。

MongoDB 数据更新可以使用 update() 函数。在执行 remove() 函数前先执行 find() 命令来判断执行的条件是否正确，这是一个比较好的习惯。

```
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```

**参数说明：**

- **query** :（可选）删除的文档的条件。

- **justOne** : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。

  > ```
  > db.col.remove({'title':'MongoDB 教程'},{'justOne',true})
  > ```

- **writeConcern** :（可选）抛出异常的级别。



> ```
> db.col.remove({'title':'MongoDB 教程'})
> WriteResult({ "nRemoved" : 2 })           # 删除了两条数据
> ```



