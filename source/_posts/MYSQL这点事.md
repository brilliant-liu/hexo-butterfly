---
title: MYSQL这点事
date: 2020-08-08 11:10:26
tags:
- MYSQL
- Oracle
  categories:
- 数据库
  cover: https://i.loli.net/2020/08/07/JxQGlO2gYjFZV1S.png

---

## 基础知识

### ACID

![image.png](https://i.loli.net/2020/08/07/vkKdytAiSO2JjBp.png)

| **ACID****属性**     | **含义**                                                     |
| -------------------- | ------------------------------------------------------------ |
| 原子性（Atomicity）  | 事务是一个原子操作单元，其对数据的修改，要么全部成功，要么全部失败。 |
| 一致性（Consistent） | 在事务开始和完成时，数据都必须保持一致状态。                 |
| 隔离性（Isolation）  | 数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的 “独立” 环境下运行。 |
| 持久性（Durable）    | 事务完成之后，对于数据的修改是永久的。                       |



### 什么是事务？

事务是逻辑上的一组操作，组成这组操作的各个逻辑单元，要么一起成功，要么一起失败。



### 并发事务处理带来的问题

| 问题                               | 含义                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| 丢失更新（LostUpdate）             | 当两个或多个事务选择同一行，最初的事务修改的值，会被后面的事务修改的值覆盖。 |
| 脏读（Dirty Reads）                | 当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。 |
| 不可重复读（Non Repeatable Reads） | 一个事务在读取某些数据后的某个时间，再次读取以前读过的数据，却发现和以前读出的数据不一致。 |
| 幻读（Phantom Reads）              | 一个事务按照相同的查询条件重新读取以前查询过的数据，却发现其他事务插入了满足其查询条件的新数据。 |



### 事务的隔离级别

为了解决上述提到的事务并发问题，数据库提供一定的事务隔离机制来解决这个问题。数据库的事务隔离越严格，并发副作用越小，但付出的代价也就越大。

| **隔离级别**            | **丢失更新** | **脏读** | **不可重复读** | **幻读** |
| ----------------------- | ------------ | -------- | -------------- | -------- |
| Read uncommitted        | ×            | √        | √              | √        |
| Read committed          | ×            | ×        | √              | √        |
| Repeatable read（默认） | ×            | ×        | ×              | √        |
| Serializable            | ×            | ×        | ×              | ×        |

> 扩展：
>
> 查看Mysql默认的隔离级别：`show variables like 'tx_isolation'; `





### 索引

是一种供服务器在表中快速查找一个行的数据库结构。合理使用索引能够大大提高数据库的运行效率。

索引也是一张表，该表保存了主键与索引字段，并指向实体表的记录

优点：

- 用以提高 SQL 语句执行的性能快速定位我们需要查找的表的内容（物理位置），提高sql语句的执行性能。
- 减少磁盘I/O  ，取数据从磁盘上取到数据缓冲区中，再交给用户。磁盘IO非常不利于表的查找速度（效率的提高）。

缺点：

- 过多的使用索引将会造成滥用。因此索引也会有它的缺点：虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件。建立索引会占用磁盘空间的索引文件。



#### **索引类型**

- **B树索引**（也叫平衡树索引，即就是什么都不写，最常用）
- **位图索引**（多用于数据仓库）



#### **B树索引**



![B数索引类型分类](https://img-blog.csdn.net/2018070120174715?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JpYmlicmF2ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





##### 唯一索引

唯一索引确保在定义索引的列中没有重复值， Oracle 自动在表的主键列上创建唯一索引 (具体列值： 索引相关列上的值必须唯一，但可以不限制NULL值。)  
语法：

> create unique index index_name on table_name (column_name);



##### 组合索引

组合索引是在表的多个列上创建的索引，索引中列的顺序是任意的， 如果 SQL 语句的 WHERE 子句中引用了组合索引的所有列或大多数列，则可以提高检索速度。

语法：

> create index index_name on table_name (column_name1，column_name2);



##### 反向键索引

反向键索引反转索引列键值的每个字节，为了实现索引的均匀分配，避免b树不平衡，通常建立在值是连续增长的列上，使数据均匀地分布在整个索引上， 创建索引时使用REVERSE关键字。

语法：

> create index index_name on table_name (column_name) reverse;



例如：

> 适用于某列值前面相同，后几位不同的情况，
> sno：        1001 1002 1003 1004 1005 1006 1007
> 索引转化：1001 2001 3001 4001 5001 6001 7001



##### 位图索引

位图索引适合创建在低基数列上， 位图索引不直接存储ROWID，而是存储字节位到ROWID的映射，节省空间占用。如果索引列被经常更新的话，不适合建立位图索引。总体来说，位图索引适合于数据仓库中，不适合OLTP中

语法：

> create bitmap index index_name on table_name (column_name);

具体列值： 不适用于经常更新的列，适用于条目多但取值类别少的列，例如性别列。



##### 基于函数的索引

基于一个或多个列上的函数或表达式创建的索引，表达式中不能出现聚合函数，不能在LOB类型的列上创建，创建时必须具有 QUERY REWRITE 权限。

语法：

> create index index_name on table_name (函数（column_name)）;

具体列值： 不能在LOB类型的列上创建，用户在该列上对该函数有经常性的要求。
例如：用户不知道存储时候姓名是大写还是小写，使用
select * from student where upper(sname)=‘TOM’；





# MySql



## Mysql的体系架构



![image.png](https://i.loli.net/2020/08/07/JxQGlO2gYjFZV1S.png)

整个MySQL Server由以下组成

- Connection Pool : 连接池组件
- Management Services & Utilities : 管理服务和工具组件
- SQL Interface : SQL接口组件
- Parser : 查询分析器组件
- Optimizer : 优化器组件
- Caches & Buffffers : 缓冲池组件
- Pluggable Storage Engines : 存储引擎
- File System : 文件系统



## 存储引擎

存储引擎就是存储数据，建立索引，更新查询数据等等技术的实现方式 。存储引擎是基于表的，而不是基于库的。

所以存储引擎也可被称为表类型。Oracle，SqlServer等数据库只有一种存储引擎。MySQL提供了插件式的存储引擎架构。所以MySQL存在多种存储引擎，可以根据需要使用相应引擎，或者编写存储引擎。

MySQL5.0支持的存储引擎包含 ： InnoDB 、MyISAM 、BDB、MEMORY、MERGE、EXAMPLE、NDB Cluster、

ARCHIVE、CSV、BLACKHOLE、FEDERATED等，其中InnoDB和BDB提供事务安全表，其他存储引擎是非事务安

全表。

> 扩展：
>
> 可以通过指定 show engines ， 来查询当前数据库支持的存储引擎 ，默认的存储引擎为InnoDB。
>
> 查看默认的存储引擎：
>
> show variables like '%storage_engine%' ；



InnoDB存储引擎不同于其他存储引擎的特点 ：

- 事务的支持

- 支持外键

  MySQL支持外键的存储引擎只有InnoDB ， 在创建外键的时候， 要求父表必须有对应的索引 ， 子表在创建外键的时候， 也会自动的创建对应的索引。

  > 扩展：
  >
  > 在创建索引时， 可以指定在删除、更新父表时，对子表进行的相应操作，包括 RESTRICT、CASCADE、SET NULL和 NO ACTION。
  >
  > RESTRICT和NO ACTION相同， 是指限制在子表有关联记录的情况下， 父表不能更新；
  >
  > CASCADE表示父表在更新或者删除时，更新或者删除子表对应的记录；
  >
  > SET NULL 则表示父表在更新或者删除的时候，子表的对应字段被SET NULL 。





## 锁

### 锁的种类划分

从对数据操作的粒度分 ：

1） 表锁：操作时，会锁定整个表。

2） 行锁：操作时，会锁定当前操作行。

从对数据操作的类型分：

1） 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响。

2） 写锁（排它锁）：当前操作没有完成之前，它会阻断其他写锁和读锁。



### 存储引擎对锁的支持

| **存储引擎** | **表级锁** | **行级锁** | **页面锁** |
| ------------ | ---------- | ---------- | ---------- |
| InnoDB       | 支持       | 支持       | 不支持     |
| MyISAM       | 支持       | 不支持     | 不支持     |
| BDB          | 支持       | 不支持     | 支持       |

> 扩展：
>
> - 表级锁
>
> 偏向MyISAM 存储引擎，开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高,并发度最低。
>
> - 行级锁
>
> 偏向InnoDB 存储引擎，开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低,并发度也最高。
>
> - 页面锁
>
> 开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。



#### MyISAM 表锁

MyISAM 在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT 等）前，会自动给涉及的表加写锁，这个过程并不需要用户干预，因此，用户一般不需要直接用 LOCK TABLE 命令给 MyISAM 表显式加锁。

> 显示加锁的方法：
>
> 加读锁 ： lock table table_name read;
>
> 加写锁 ： lock table table_name write；

总结：

1） 对MyISAM 表的读操作，不会阻塞其他用户对同一表的读请求，但会阻塞当前用户对同一表的写请求；

2） 对MyISAM 表的写操作，则会阻塞其他用户对同一表的读和写操作；

简而言之，就是读锁会阻塞写，但是不会阻塞读。而写锁，则既会阻塞读，又会阻塞写。



> 扩展:
>
> 查看锁的竞争情况` show open tables；`
>
> ![image.png](https://i.loli.net/2020/08/07/zKBYx5rg4QEbFuI.png)
>
> In_user : 表当前被查询使用的次数。如果该数为零，则表是打开的，但是当前没有被使用。
>
> Name_locked：表名称是否被锁定。名称锁定用于取消表或对表进行重命名等操作。
>
> `show status like 'Table_locks%';`
>
> ![image.png](https://i.loli.net/2020/08/07/P9Uq4XKklGrBfFm.png)
>
> Table_locks_immediate ： 指的是能够立即获得表级锁的次数，每立即获取锁，值加1。
>
> Table_locks_waited ： 指的是不能立即获取表级锁而需要等待的次数，每等待一次，该值加1，此值高说明存在着较为严重的表级锁争用情况。



#### Innodb 行锁

行锁特点 ：偏向InnoDB 存储引擎，开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低,并发度也最高。

InnoDB 与 MyISAM 的最大不同有两点：一是支持事务；二是 采用了行级锁。

##### InnoDB 的行锁模式

- 共享锁（S）

  又称为读锁，简称S锁，共享锁就是多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改。

- 排他锁（X）

  又称为写锁，简称X锁，排他锁就是不能与其他锁并存，如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁，包括共享锁和排他锁，但是获取排他锁的事务是可以对数据就行读取和修改。

> 对于UPDATE、DELETE和INSERT语句，InnoDB会自动给涉及数据集加排他锁（X)；
>
> 对于普通SELECT语句，InnoDB不会加任何锁；
>
>
>
> 显示添加共享锁和排他锁
>
> 共享锁（S）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE
>
> 排他锁（X) ：SELECT * FROM table_name WHERE ... FOR UPDATE



##### **无索引行锁升级为表锁**

如果不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，实际效果跟表锁一样。

![image.png](https://i.loli.net/2020/08/07/prgUZvQG1LC2O5s.png)



> 扩展：
>
> 查看当前表的索引 ： `show index from test_innodb_lock ;`
>
>
>
> 索引失效的情况：
>
> - like 以%开头，索引无效；当like前缀没有%，后缀有%时，索引有效。
> - or语句前后没有同时使用索引。当or左右查询字段只有一个是索引，该索引失效，只有当or左右查询字段均为索引时，才会生效
> - 组合索引，不是使用第一列索引，索引失效。
> - 存在类型转换，索引失效。
>
> 例如：如果列类型是varchar，那一定要在条件中将数据使用引号引用起来,否则不使用索引。
>
> - 如果mysql估计使用全表扫描要比使用索引快,则不使用索引
> - 不等于(！= ，<> )，EXISTS，not in,is  not null,>,<都会失效，in（in里面包含了子查询）（非主键索引）



### 间隙锁

当我们用范围条件，而不是使用相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据进行加锁；

对于键值在条件范围内但并不存在的记录，叫做"**间隙（GAP）**" ， InnoDB也会对这个 "间隙" 加锁，这种锁机制就是所谓的 间隙锁（Next-Key锁） 。

![image.png](https://i.loli.net/2020/08/07/F5ZXAGy6Q1SlJoh.png)



### 关于锁方面的优化

- 尽可能让所有数据检索都能通过索引来完成，避免无索引行锁升级为表锁。
- 合理设计索引，尽量缩小锁的范围
- 尽可能减少索引条件，及索引范围，避免间隙锁
- 尽量控制事务大小，减少锁定资源量和时间长度
- 尽可使用低级别事务隔离（但是需要业务层面满足需求）



## Mysql查询缓存

开启Mysql的查询缓存，当执行完全相同的SQL语句的时候，服务器就会直接从缓存中读取结果，当数据被修改，之前的缓存会失效，修改比较频繁的表不适合做查询缓存。

![image.png](https://i.loli.net/2020/08/07/qk217v5m8lLuxAZ.png)

1. 客户端发送一条查询给服务器；
2. 服务器先会检查查询缓存，如果命中了缓存，则立即返回存储在缓存中的结果。否则进入下一阶段；
3. 服务器端进行SQL解析、预处理，再由优化器生成对应的执行计划；
4. MySQL根据优化器生成的执行计划，调用存储引擎的API来执行查询；
5. 将结果返回给客户端。

### 相关命令

1. 查看当前的MySQL数据库是否支持查询缓存

   > SHOW VARIABLES LIKE 'have_query_cache';

2. 查看当前MySQL是否开启了查询缓存

   > SHOW VARIABLES LIKE 'query_cache_type';

3. 查看查询缓存的占用大小

   > SHOW VARIABLES LIKE 'query_cache_size';

4. 查看查询缓存的状态变量

   > SHOW STATUS LIKE 'Qcache%'

5. **开启查询缓存**

   MySQL的查询缓存默认是关闭的，需要手动配置参数 query_cache_type ， 来开启查询缓存。query_cache_type该参数的可取值有三个 。（在 /usr/my.cnf 配置中，增加改配置 ）

   | 值         | 含义                                                         |
      | ---------- | ------------------------------------------------------------ |
   | OFF 或 0   | 查询缓存功能关闭                                             |
   | ON 或 1    | 查询缓存功能打开，SELECT的结果符合缓存条件即会缓存，否则，不予缓存，显式指定SQL_NO_CACHE，不予缓存 |
   | DEMAND或 2 | 查询缓存功能按需进行，显式指定 SQL_CACHE 的SELECT语句才会缓存；其它均不予缓存 |

### 查询缓存失效的情况

1. SQL 语句不一致的情况， 要想命中查询缓存，查询的SQL语句必须一致。

2. 当查询语句中有一些不确定的时，则不会缓存。如 ： now() , current_date() , curdate() , curtime() , rand() ,uuid() , user() , database() 。

3. 不使用任何表查询语句。

   例如：select 'A';

4. 在存储的函数，触发器或事件的主体内执行的查询。



## Mysql日志

在任何一种数据库中，都会有各种各样的日志，记录着数据库工作的方方面面，以帮助数据库管理员追踪数据库曾经发生过的各种事件。MySQL 也不例外，在 MySQL 中，主要有 4 种不同的日志。

- errlog 错误日志
- BINLOG  二进制日志
- general log 查询日志
- slow query log 慢查询日志
- redo 重做日志
- undo 回滚日志
- relay log 中继日志



### 错误日志

它记录了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，可以首先查看此日志。

该日志是默认开启的 ， 默认存放目录为 mysql 的数据目录（var/lib/mysql）, 默认的日志文件名为hostname.err（hostname是主机名）。

> 查看日志位置指令: `show variables like 'log_error%'; `



### 二进制文件（BINLOG）

二进制日志（BINLOG）记录了所有的 DDL（数据定义语言）语句和 DML（数据操纵语言）语句，但是不包括数据查询语句。**此日志对于灾难时的数据恢复起着极其重要的作用**，**MySQL的主从复制**， 就是通过该binlog实现

的。

二进制日志，默认情况下是没有开启的，需要到MySQL的配置文件中开启，并配置MySQL日志的格式。

> 通过配置参数 `log-bin[=name]` 可以启动二进制日志。如果不指定name,则默认二进制日志文件名为主机名，后缀名为二进制日志的序列号



#### 日志格式

- **STATEMENT**

  该日志格式在日志文件中记录的都是SQL语句（statement），每一条对数据进行修改的SQL都会记录在日志文件中。通过Mysql提供的mysqlbinlog工具，可以清晰的查看到每条语句的文本。主从复制的时候，从库（slave）会将日志解析为原文本，并在从库重新执行一次。

- **ROW**

  该日志格式在日志文件中记录的是每一行的数据变更，而不是记录SQL语句。比如，执行SQL语句 ： update tb_book set status='1' , 如果是STATEMENT 日志格式，在日志中会记录一行SQL文件； 如果是ROW，由于是对全表进行更新，也就是每一行记录都会发生变更，ROW 格式的日志中会记录每一行的数据变更。

- **MIXED**

  这是目前MySQL默认的日志格式，即混合了STATEMENT 和 ROW两种格式。默认情况下采用STATEMENT，但是在一些特殊情况下采用ROW来进行记录。MIXED 格式能尽量利用两种模式的优点，而避开他们的缺点。

  > 特殊情形：
  >
  > - 表的存储引擎为NDB，这时对表的DML操作都会以ROW格式记录。
  > - 使用了UUID()、USER()、CURRENT_USER()、FOUND_ROW()、ROW_COUNT()等不确定函数。
  > - 使用了INSERT DELAY语句。
  > - 使用了用户定义函数（UDF）。
  > - 使用了临时表（temporary table）。



#### BINLOG的作用

- **恢复(recovery)**

  某些数据的恢复需要二进制日志，如当一个数据库全备文件恢复后，我们可以通过二进制的日志进行 `point-in-time`的恢复

- **复制(replication)**

  通过复制和执行二进制日志使得一台远程的 MySQL 数据库(一般是slave 或者 standby) 与一台MySQL数据库(一般为master或者primary) 进行实时同步

- **审计(audit)**

  用户可以通过二进制日志中的信息来进行审计，判断是否有对数据库进行注入攻击



#### 日志的删除

对于比较繁忙的系统，由于每天生成日志量大 ，这些日志如果长时间不清楚，将会占用大量的磁盘空间。下面是几种删除日志的常见方法 。

- ` Reset Master `

  通过 Reset Master 指令**删除全部 binlog 日志**，删除之后，日志编号，将从 xxxx.000001重新开始 。

- `purge master logs to 'mysqlbin.******' `

  执行指令 purge master logs to 'mysqlbin.******' ，该命令将删除 ****** 编号之前的所有日志

- `purge master logs before 'yyyy-mm-dd hh24:mi:ss' `

  执行指令 purge master logs before 'yyyy-mm-dd hh24:mi:ss' ，该命令将删除日志为 "yyyy-mm-dd hh24:mi:ss" 之前产生的所有日志 。

- 设置参数 `--expire_logs_days=#`

  此参数的含义是设置日志的过期天数， 过了指定的天数后日志将会被自动删除，这样将有利于减少DBA 管理日志的工作量。



> 扩展：
>
> 由于日志以二进制方式存储，不能直接读取，需要用mysqlbinlog工具来查看，语法如下 ：
>
> `mysqlbinlog log-file；`
>
> 如果日志格式是 ROW , 直接查看数据 , 是查看不懂的 ; 可以在mysqlbinlog 后面加上参数 -vv
>
> `mysqlbinlog -vv mysqlbin.000002`



### 查询日志

查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。默认情况下， 查询日志是未开启的。

> \#该选项用来开启查询日志 ， 可选值 ： 0 或者 1 ； 0 代表关闭， 1 代表开启
>
> general_log=1
>
> \#设置日志的文件名 ， 如果没有指定， 默认的文件名为 host_name.log
>
> general_log_file=file_name

```mysq
mysql> show variables like "general_log%";
+------------------+---------------------------------+
| Variable_name    | Value                           |
+------------------+---------------------------------+
| general_log      | OFF                             |
| general_log_file | /var/lib/mysql/baa98165b538.log |
+------------------+---------------------------------+
2 rows in set (0.01 sec)

```



### 慢查询日志

慢查询日志记录了所有执行时间超过参数 `long_query_time` 设置值并且扫描记录数不小于`min_examined_row_limit `的所有的SQL语句的日志。`long_query_time` 默认为 10 秒，最小为 0， 精度可以到微秒。

查看慢查询日志的配置：

```mysql
mysql> show variables like "%slow%";
+---------------------------+--------------------------------------+
| Variable_name             | Value                                |
+---------------------------+--------------------------------------+
| log_slow_admin_statements | OFF                                  |
| log_slow_extra            | OFF                                  |
| log_slow_slave_statements | OFF                                  |
| slow_launch_time          | 2                                    |
| slow_query_log            | OFF                                  |
| slow_query_log_file       | /var/lib/mysql/baa98165b538-slow.log |
+---------------------------+--------------------------------------+
6 rows in set (0.01 sec)
```



慢查询日志默认是关闭的 。可以通过两个参数来控制慢查询日志 ：

> \# 该参数用来控制慢查询日志是否开启， 可取值： 1 和 0 ， 1 代表开启， 0 代表关闭
>
> slow_query_log=1
>
> \# 该参数用来指定慢查询日志的文件名
>
> slow_query_log_file=slow_query.log
>
> \# 该选项用来配置查询的时间限制， 超过这个时间将认为值慢查询， 将需要进行日志记录， 默认10s
>
> long_query_time=10



> 扩展：
> 和错误日志、查询日志一样，慢查询日志记录的格式也是纯文本，可以被直接读取。
>
> 如果慢查询日志内容很多， 直接查看文件，比较麻烦， 这个时候可以借助于mysql自带的 `mysqldumpslow `工具， 来对慢查询日志进行分类汇总。

> 优秀参考地址： https://juejin.im/post/6844903662888697864



### relay log

从数据库Slave服务的I/O线程从主数据库Master服务的二进制日志中读取数据库的更改记录并写入到中继日志中，然后在Slave数据库执行修改操作。这就是中继日志Relay Log。

> 具体的参数可参考：https://cn-blogs.cn/archives/359.html



### redo log

redo log 通常是物理日志，记录的是数据页的物理修改，而不是某一行或某几行修改成怎样怎样，它用来恢复提交后的物理数据页(恢复数据页，且只能恢复到最后一次提交的位置)。

redo log是InnoDB存储引擎层的日志，又称重做日志文件，用于记录事务操作的变化，记录的是数据修改之后的值，不管事务是否提交都会记录下来。

redo能够确保事务的持久性。防止在发生故障的时间点，尚有脏页未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。



> redo log和binlog区别
>
> - redo log是属于innoDB层面，binlog属于MySQL Server层面的，这样在数据库用别的存储引擎时可以达到一致性的要求。
> - redo log是物理日志，记录该数据页更新的内容；binlog是逻辑日志，记录的是这个更新语句的原始逻辑
> - redo log是循环写，日志空间大小固定；binlog是追加写，是指一份写到一定大小的时候会更换下一个文件，不会覆盖。
> - binlog可以作为恢复数据使用，主从复制搭建，redo log作为异常宕机或者介质故障后的数据恢复使用。



### undo log

保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读



## Mysql集群架构

### 主从架构

- 一主一丛

- 一主多从

- 多主一从

- 双主复制

  双主复制，也就是互做主从复制，每个master既是master，又是另外一台服务器的slave。这样任何一方所做的变更，都会通过复制应用到另外一方的数据库中。

- 级联复制

  级联复制模式下，部分slave的数据同步不连接主节点，而是连接从节点。因为如果主节点有太多的从节点，就会损耗一部分性能用于replication，那么我们可以让3~5个从节点连接主节点，其它从节点作为二级或者三级与从节点连接，这样不仅可以缓解主节点的压力，并且对数据一致性没有负面影响。



### 主从复制原理

主从复制：复制是指将主数据库的DDL 和 DML 操作通过**二进制日志（BINGLOG）**传到从库服务器中，然后在从库上对这些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步。

MySQL支持一台主库同时向多台从库进行复制， 从库同时也可以作为其他从服务器的主库，实现链状复制。

![image.png](https://i.loli.net/2020/08/08/bhECGX1FIJ5Lk8m.png)

从上层来看，复制分成三步：

1. Master 主库在事务提交时，会把数据变更作为时间 Events 记录在二进制日志文件 Binlog 中。
2. 主库推送二进制日志文件 Binlog 中的日志事件到从库的中继日志 Relay Log 。
3. slave重做中继日志中的事件，将改变反映它自己的数据。

详细细节描述

1. 主节点 binary log dump 线程
   当从节点连接主节点时，主节点会创建一个log dump 线程，用于发送bin-log的内容。在读取bin-log中的操作时，此线程会对主节点上的bin-log加锁，当读取完成，甚至在发动给从节点之前，锁会被释放。
2. 从节点I/O线程
   当从节点上执行`start slave`命令之后，从节点会创建一个I/O线程用来连接主节点，请求主库中更新的bin-log。I/O线程接收到主节点binlog dump 进程发来的更新之后，保存在本地relay-log中。
3. 从节点SQL线程
   SQL线程负责读取relay log中的内容，解析成具体的操作并执行，最终保证主从数据的一致性。



### 主从复制的优点

- 读写分离
  在开发工作中，有时候会遇见某个sql 语句需要锁表，导致暂时不能使用读的服务，这样就会影响现有业务，使用主从复制，让主库负责写，从库负责读，这样，即使主库出现了锁表的情景，通过读从库也可以保证业务的正常运作。

- 数据实时备份

  当系统中某个节点发生故障时，可以方便的故障切换

- 高可用HA

- 架构扩展
  随着系统中业务访问量的增大，如果是单机部署数据库，就会导致I/O访问频率过高。有了主从复制，增加多个数据存储节点，将负载分布在多个从节点上，降低单机磁盘I/O访问的频率，提高单个机器的I/O性能。





## MySql的性能优化

### 分页优化

1. 创建一张表用来记录日志表的总数据量

   ```my
   create table log_counter( logcount bigint not null )engine = innodb default CHARSET = utf8;
   ```

2. 在每次插入数据之后，更新该表

3. 在进行分页查询时, 获取总记录数，从该表中查询既可。



### limit优化

在进行分页时，一般通过创建覆盖索引，能够比较好的提高性能。一个非常常见，而又非常头疼的分页场景就是"limit 1000000,10" ，此时MySQL需要搜索出前1000010 条记录后，仅仅需要返回第 1000001 到 1000010 条记录，前1000000 记录会被抛弃，查询代价非常大。当点击比较靠后的页码时，就会出现这个问题，查询效率非常慢。

sql优化前的语句：

```mysq
select * from operation_log limit 3000000 , 10;
```

优化的SQL：

```mysq
select * from operation_log t , (select id from operation_log order by id limit 3000000,10) b where t.id = b.id ;
```

优化思路： 先查出需要分页查询的主键，在和原表进行关联查。



### 索引优化

根据查询的条件，分别创建索引，多个条件的时候，为每个条件分别创建单独索引和组合索引



### 排序优化

在查询数据时，如果业务需求中需要我们对结果内容进行了排序处理 , 这个时候,我们还需要对排序的字段建立适当的索引, 来提高排序的效率 。



### 读写分离优化

对于读写分离的实现，可以通过Spring AOP 来进行动态的切换数据源。



### 缓存优化

可以在业务系统中使用二级缓存，例如redis，ehcache来做缓存，缓存一些基础性的数据，来降低关系型数据库的压力，提高访问效率。





# Oracle





## Oracle相关概念



**Oracle数据库存储结构：**



![Oracle 存储结构](https://pic002.cnblogs.com/images/2012/311516/2012081214222318.png)



从逻辑的角度来看:

一个数据库（database）下面可以分多个表空间（tablespace）；

一个表空间下面又可以分多个段（segment）；一个数据表要占一个段（segment），一个索引也要占一个段（segment ）。

一个段（segment）由多个 区间（extent）组成，那么一个区间又由一组连续的数据块（data block）组成。

这连续的数据块是在逻辑上是连续的，有可能在物理磁盘上是分散。



> 扩展：
>
> **表空间**：
>
> ORACLE数据库被划分成称作为表空间的逻辑区域——形成ORACLE数据库的[逻辑结构]。
>
> 一个ORACLE数据库能够有一个或多个表空间,而一个表空间则对应着一个或多个物理的数据库文件。表空间是ORACLE数据库恢复的最小单位,容纳着许多数据库实体,如表、视图、索引、聚簇、回退段和临时段等。
>
> 每个ORACLE数据库均有[SYSTEM]表空间,这是数据库创建时自动创建的。SYSTEM表空间必须总要保持联机,因为其包含着数据库运行所要求的基本信息。
>
> **使用表空间的好处**
>
> 表空间的作用能帮助DBA用户完成以下工作:
> 1.决定数据库实体的空间分配;
> 2.设置数据库用户的空间份额;
> 3.控制数据库部分数据的可用性;
> 4.分布数据于不同的设备之间以改善性能;
> 5.备份和恢复数据。



> **Schema的概念**
>
> 实际上，schema就是数据库对象的集合，这个集合包含了各种对象如：表、视图、存储过程、索引等。
>
> 如果把database看作是一个仓库，仓库很多房间（schema），一个schema代表一个房间，table可以看作是每个房间中的储物柜，user是每个schema的主人，有操作数据库中每个房间的权利，就是说每个数据库映射user有每个schema（房间）的钥匙。
>
>







# Oracle 和Mysql的对比



## Mysql和Oracle中的事务隔离级别

mysql默认的事务处理级别是'REPEATABLE-READ',也就是可重复读

oracle数据库支持READ COMMITTED 和 SERIALIZABLE这两种事务隔离级别。

默认系统事务隔离级别是READ COMMITTED,也就是读已提交





## MySQL和Orcale数据库的区别

Oracle与mysql区别：

**1.Oracle有表空间的概念，mysql没有表空间。**

**2.自动增长的数据类型处理**

> **MYSQL**有自动增长的数据类型，插入记录时不用操作此字段，会自动获得数据值。
>
> **ORACLE**没有自动增长的数据类型，需要建立一个自动增长的序列号，插入记录时要把序列号的下一个值赋于此字段。
>
> 创建序列的方法：
>
> CREATE SEQUENCE  序列号的名称(最好是表名+序列号标记)
>
> INCREMENT BY 1
>
> START WITH 1
>
> MAXVALUE 99999
>
> CYCLE NOCACHE;
>
> 其中最大的值按字段的长度来定，如果定义的自动增长的序列号NUMBER(6)，最大值为999999
>
> INSERT语句插入这个字段值为：序列号的名称.NEXTVAL

**3.单引号的处理**

MYSQL里可以用双引号包起字符串，

ORACLE里只可以用单引号包起字符串。在插入和修改字符串前必须做单引号的替换：把所有出现的一个单引号替换成两个单引号。

> Oracle中单引号有2个作用：
>
> - 第一,  是作为字符串由单引号包围；例如  select  ‘asd’  from  dual；输出:asd
> - 第二，作为转义，从语句的第二个单引号开始作为转义。例如  select  '''' from  dual;输出：‘

**4.分页的SQL语句的处理**

- MYSQL处理翻页的SQL语句比较简单，用LIMIT开始位置，记录个数；

```mysql
-- 语法：SELECT * FROM table LIMIT [offset,] rows | rows OFFSET offset
-- 返回前5行
select * from table limit 5; 
-- 同上，返回前5行
select * from table limit 0,5; 
-- 返回6-15行
select * from table limit 5,10; 
```

- ORACLE处理翻页,使用每个结果集只有一个ROWNUM字段标明它的位置。

```sql
-- 语句一：

SELECT ID, [FIELD_NAME,...] 
FROM TABLE_NAME 
WHERE ID IN ( 
    SELECT ID 
    FROM (SELECT ROWNUM AS NUMROW, ID 
          FROM TABLE_NAME 
          WHERE 条件1 
          ORDER BY 条件2) 
    WHERE NUMROW > 80 AND NUMROW < 100 ) 
ORDER BY 条件3;

-- 语句二：
select * from (
    select row_.*, rownum rownum_ 
    from (select person_id, chn_name, chn_firstname_py from t_pbase_info) row_ 
    where rownum <=20
) where rownum_ >=11
```

**8.字符串的模糊比较**

- MYSQL里用字段名like%‘字符串%’。

  > like '字符串%'，对于索引字段是可能会走索引的。

- ORACLE里也可以用字段名like%‘字符串%’  但这种方法不能使用索引，速度不快。

  用字符串比较函数  instr(字段名，‘字符串’)>0会得到更精确的查找结果。



**6.日期字段的处理**

MYSQL日期字段分DATE和TIME两种.

ORACLE日期字段只有DATE，包含年月日时分秒信息，用当前数据库的系统时间为SYSDATE，精确到秒。

> 扩展：
>
> 1：用字符串转换成日期型函数TO_DATE(‘2001-10-23’,’YYYY-MM-DD’)
>
> 2：TO_CHAR(‘2001-10-23’,’YYYY-MM-DD HH24:MI:SS’)
>
> Oracle获取当前时间： sysdate；
>
> MySql中获取当前时间：now();
>
>
>
> 日期字段的数学运算公式有很大的不同。
>
> MYSQL找到离当前时间7天用DATE_FIELD_NAME > SUBDATE(NOW()，INTERVAL 7 DAY)；
>
> ORACLE找到离当前时间7天用 DATE_FIELD_NAME >SYSDATE - 7;

**7.空字符的处理**

- MYSQL中有空值（NULL）字段也有空('')的内容。

  > 扩展：
  >
  > **mysql中空字符串（”）和空值（null）之间有区别**
  >
  > - NULL是指没有值，而”则表示值是存在的，只不过是个空值。
  > - NOT NULL：是可插入‘’数据的，但不能插入null;
  > - 在查询上，null和‘’的查询结果是不一样的，查询空值null的语句，只能查询字段是空值null的结果，而查询‘’的语句，只能查询字段值为‘’的结果；而且比较字符 ‘=’’>’ ‘<’ ‘<>’是不能用于查询null。如果需要查询空值（null），需使用is null 和is not null。
  > - 空值（null）是不能参与任何计算，因为空值参与任何计算都为空。如果想参与运算，可使用IFNULL函数，而‘’是可参与运算的。
  >
  > ```mysql
  > IFNULL(expr1,expr2)
  > -- 如果expr1不是NULL，IFNULL()返回expr1，否则它返回expr2。IFNULL()返回一个数字或字符串值
  > ```
  >
  >
  >
  > **Oracle中空字符串（”）和空值（null）没有区别**
  >
  > Oracle底层会将‘’转为NULL,所以针对NOT NULL字段，‘’和NULL都不允许插入。
>
>



**8.字符串的模糊比较**

MYSQL里用字段名like%‘字符串%’，ORACLE里也可以用字段名like%‘字符串%’但这种方法不能使用索引，速度不快，用字符串比较函数instr(字段名，‘字符串’)>0会得到更精确的查找结果。





