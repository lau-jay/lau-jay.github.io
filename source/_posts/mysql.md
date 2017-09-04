---
title: mysql
date: 2017-07-31 22:37:36
categories:
tags: DB
---
MySQL Engine:

- MyISAM: 默认引擎，不支持事务，效率较好，一个table会有三个同名文件结尾分别为: frm, MYD, MYI。兼容好，直接复制文件就能移动数据库。
- InnoDB: 支持事务，一个table一个同名文件结尾为: frm。由于所有数据库的资料会放一个存储空间内所以这个空间里InnoDB限制不超过两百万个(后续版本待确认)。
- MEMORY: 记录和索引存内存里，读写效率高，费内存，只有一个frm用于存储table结构。


操作系统限制：

- MYISAM可以避免系统文件大小限制
- InnoDB可以避免档案数量限制
- 如果大小和数量都有限制需要改系统设置

时间数据类型:

- TIMESTAMP 具备'时区'特性，可以存储从`1970-01-01 00:00:00`到目前经过的秒数，包含完整的年, 月, 日与时分秒，不过使用的存储空间只有4bytes。
- DATATIME 同TIMESTAMP比多了倍的存储开销，需要8bytes

所以存时间数据的时候，可能会由于时区换算导致超过时间底线即`1970-01-01 00:00:00`。
查看数据库关于时区的设定 `SELECT @@GLOBAL.TIME_ZONE, @@SESSION.TIME_ZONE;`
`SET SESSION TIME_ZONE = '-08:00'`北京时间比格林威治时间早8个小时
`SET GLOBAL TIME_ZONE = '+00:00'`

MySQL 限制'CURRENT_TIMESTAMP'一个表格中只能在一个字段出现

触发器:

- 每个表格最多可以建立六种trigger，每种只能有一个

  BEFORE INSERT|AFTER INSERT
  BEFORE UPDATE|AFTER UPDATE
  BEFORE DELETE|AFTER DELETE

#### 事务
指作为单个逻辑工作单元执行的一系列操作，而这些逻辑工作单元需要具有原子性，一致性，隔离性和持久性四个属性，统称为ACID特性

#### ACID
* 原子性(Atomicity)：事务中的所有元素作为一个整体提交或回滚，事务的个元素是不可分的，事务是一个完整操作。
* 一致性(Consistemcy)：事物完成时，数据必须是一致的，也就是说，和事物开始之前，数据存储中的数据处于一致状态。保证数据的无损。
* 隔离性(Isolation)：对数据进行修改的多个事务是彼此隔离的。这表明事务必须是独立的，不应该以任何方式以来于或影响其他事务。
* 持久性(Durability)：事务完成之后，它对于系统的影响是永久的，该修改即使出现系统故障也将一直保留，真实的修改了数据库

#### SQL语句

    BEGIN TRANSACTION 
    COMMIT 
    ROLLBACK


#### drop、delete、truncate
* delete和truncate只删除表的数据不删除表的结构
* delete操作会放到rollback segment中，事务提交后猜生效，会触发trigger
* truncate，drop操作立即生效，原数据不放倒rollback segment中，不能回滚，操作不触发trigger
* 不需要一张表的时候用drop
* 去除数据但保留表用truncate
* 删除部分数据行用delete

