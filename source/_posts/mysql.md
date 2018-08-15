---
title: mysql
date: 2017-07-31 22:37:36
categories:
tags: DB
---
## MySQL Engine:

- MyISAM: 默认引擎，不支持事务，效率较好，一个table会有三个同名文件结尾分别为: frm, MYD, MYI。兼容好，直接复制文件就能移动数据库。
- InnoDB: 支持事务，一个table一个同名文件结尾为: frm。由于所有数据库的资料会放一个存储空间内所以这个空间里InnoDB限制不超过两百万个(后续版本待确认)。
- MEMORY: 记录和索引存内存里，读写效率高，费内存，只有一个frm用于存储table结构。


## 操作系统限制：

- MYISAM可以避免系统文件大小限制
- InnoDB可以避免档案数量限制
- 如果大小和数量都有限制需要改系统设置

## 时间数据类型

- TIMESTAMP 具备'时区'特性，可以存储从`1970-01-01 00:00:00`到目前经过的秒数，包含完整的年, 月, 日与时分秒，不过使用的存储空间只有4bytes。
- DATATIME 同TIMESTAMP比多了倍的存储开销，需要8bytes

所以存时间数据的时候，可能会由于时区换算导致超过时间底线即`1970-01-01 00:00:00`。
查看数据库关于时区的设定 `SELECT @@GLOBAL.TIME_ZONE, @@SESSION.TIME_ZONE;`
`SET SESSION TIME_ZONE = '-08:00'`北京时间比格林威治时间早8个小时
`SET GLOBAL TIME_ZONE = '+00:00'`

MySQL 限制'CURRENT_TIMESTAMP'一个表格中只能在一个字段出现

## 触发器

- 每个表格最多可以建立六种trigger，每种只能有一个

  BEFORE INSERT|AFTER INSERT
  BEFORE UPDATE|AFTER UPDATE
  BEFORE DELETE|AFTER DELETE

## 事务
指作为单个逻辑工作单元执行的一系列操作，而这些逻辑工作单元需要具有原子性，一致性，隔离性和持久性四个属性，统称为ACID特性。

### ACID

- 原子性(Atomicity)：事务中的所有元素作为一个整体提交或回滚，事务的个元素是不可分的，事务是一个完整操作。
- 一致性(Consistemcy)：事物完成时，数据必须是一致的，也就是说，和事物开始之前，数据存储中的数据处于一致状态。保证数据的无损。
- 隔离性(Isolation)：对数据进行修改的多个事务是彼此隔离的。这表明事务必须是独立的，不应该以任何方式以来于或影响其他事务。
- 持久性(Durability)：事务完成之后，它对于系统的影响是永久的，该修改即使出现系统故障也将一直保留，真实的修改了数据库

非并发下事务串行执行，只要满足原子，就能满足一致性

并发下，除了原子外还要满足隔离性，才能满足一致性

### 事务的隔离级别

|隔离级别|	脏读|	不可重复读|	幻影读|
|---------|------|-----------| -------|
|未提交读|	YES | YES |	YES|
|提交读	|NO	|YES	|YES|
|可重复读|	NO	|NO	|YES|
|可串行化|	NO	|NO	|NO|



* 脏读指两个事务，T1修改，T2读，如果T1的在T2读完后回滚了。那么T2读到的就是错误的数据，所以叫脏读。只要隔离性高于未提交读就能避免
* 不可重复读是指两个事务 T1修改T2读，如果T2读在T1修改之前，那么T2再次读，数据不一致了。叫做不可重复读。只要隔离性高于提交读，就可以避免了。
* 读取某个范围的数据，T2 在这个范围内插入新的数据，T1 再次读取这个范围的数据，此时读取的结果和和第一次读取的结果不同。这个就需要最高的串行化才能避免了

MySQL默认是可重复读隔离级别的


## SQL语句
### DDL

* CREATE: 创建数据库和表等对象
* DROP: 删除数据库和表等对象
* ALTER: 修改数据库和表等对象的结构

### DML

* SELECT: 查询表中的数据
* INSERT: 向表中插入新数据
* UPDATE: 更新表中的数据
* DELETE: 删除表中的数据

### DCL

* COMMIT: 确认对数据库中的数据进行的变更
* ROLLBACK: 取消对数据库中的数据进行的变更
* GRANT: 赋予用户操作权限
* REVOKE: 取消用户的操作权限


### DROP、DELETE、TRUNCATE

* DELETE和TRUNCATE只删除表的数据不删除表的结构
* DELETE操作会放到ROLLBACK SEGMENT中，事务提交后猜生效，会触发Trigger
* TRUNCATE，DROP操作立即生效，原数据不放到ROLLBACK SEGMENT中，不能回滚，操作不触发trigger
* 不需要一张表的时候用DROP
* 去除数据但保留表用TRUNCATE
* 删除部分数据行用DELETE


## show warnings;

* 执行完sql file 有时候会有warnings, 可以用这个命令查看, 有个问题是会被后面正常执行的给覆盖掉
