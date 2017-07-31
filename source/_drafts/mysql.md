---
title: mysql
date: 2017-08-01 22:37:36
categories:
tags: db
---
MySQL Engine:

- MyISAM: 默认引擎，不支持事务，效率较好，一个table会有三个同名文件结尾分别为: frm, MYD, MYI。兼容好，直接复制文件就能移动数据库。
- InnoDB: 支持事务，一个table一个同名文件结尾为: frm。由于所有数据库的资料会放一个存储空间内所以这个空间里InnoDB限制不超过两百万个(后续版本待确认)。
- MEMORY: 记录和索引存内存里，读写效率高，费内存，只有一个frm用于存储table结构。


操作系统限制：

- MYISAM可以避免系统文件大小限制
- InnoDB可以避免档案数量限制
- 如果大小和数量都有限制需要改系统设置

