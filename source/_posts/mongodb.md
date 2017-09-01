---
title: mongoDB
date: 2017-08-27 13:40:17
tags: DB
---
数据库中有两大体系，SQL和NoSQL，对于概念的定义其实不好说清楚，我比较认同的一种概念是:
SQL指关系型数据库，维护数据的持久性的是单个系统。比如MySQL数据库，尽管可以部署出集群，但本质上每个单元确实是单个系统。
NoSQL就是非关系型，保持开放，利用已有的可用的工具和方法去管理数据.

这篇笔记记录MongoDB这一文档型NoSQL数据库的基本概念与使用中学到的一些知识。

* MongoDB有着与熟知的‘数据库’（database，对于MySQL就是‘schema’）一样的概念。在一个MongoDB的实例中有
若干个数据库或者一个也没有，不过这里的每一个数据库都是高层次的容器，用来储存其他的所有数据。

* 一个数据库可以有若干‘集合’（collection, 吐槽句Azure是按collection收费的，不知道AWS怎么收费），或者一个也没有。
集合和传统概念中的‘表’有着足够多的共同点，可认为这两者是一样的东西。
集合由若干‘文档’（document）组成，也可以为空。可以认为这里的文档就是‘行’。

* 文档又由一个或者更多个‘域’（field）组成，域就像是‘列’。

* ‘索引’（index）在MongoDB中的意义就如同索引在RDBMS中一样。

* ‘游标’（cursor）和以上5个概念不同。关于游标有一点很重要，就是每当向MongoDB索要数据时，
它总是返回一个游标。基于游标可以做诸如计数或是直接跳过之类的操作，而不需要真正去读数据

# shell 操作
对于一个数据库最快的熟悉方式是直接在提供的shell里操作，简单记录下shell操作的一些命令。

`use dbname`如果不存在会自动创建该数据库
操作对象db来执行针对当前数据库的操作，首先直接db.help()就能看到所有的命令的帮助。
使用`db.stats()或者db.getCollectionNames()`可以看到当前数据库的collection数目和一些其他信息。
详细操作可以看文档，所以不做多记录。

集合是无模式的，不需要专门去创建它: `db.pyclear.insert({name: 'pyclear', gender: 'm'})`
这样就会在当前数据库中创建collection并且在该collection中插入文档,在MongoDB内部，数据是以二进制的串行JSON格式存储的。

关于二进制串行JSON有个特有的称呼BSON,官方文档的解释如下:

```
MongoDB represents JSON documents in binary-encoded format called BSON behind the scenes.
BSON extends the JSON model to provide additional data types, ordered fields,
and to be efficient for encoding and decoding within different languages.
```



