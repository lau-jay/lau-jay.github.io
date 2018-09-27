---
title: Tornado执行阻塞函数续
date: 2018-09-27 08:13:17
categories: python
tags: Tornado
---
上一篇说到了Tornado执行阻塞函数的姿势, 并且在上次使用的时候发现了文档bug提交issue,
原本以为会从代码层面更改。。结果亮瞎眼。我直接复制之前UPDATE

UPDATE: 这里其实文档有错，在我提的issue之后，文档更为如下，一共需要三个参数
```
async def call_blocking():
    await IOLoop.current().run_in_executor(None, blocking_func, args)
```
然后我提了说这个做法不够优雅，实现run_in_executor的老外说，虽然是有点尴尬，
不过asyncio也是这么处理的，那没辙就这样吧.感兴趣的可以看[这里](https://github.com/tornadoweb/tornado/issues/2493)

我在上篇写到了其实现简单粗暴，另开线程。这篇要分享的一个坑就是与此相关的。

最近写的一个东西，使用了很早之前的torndb, 为了不阻塞，使用这个函数去跑，由于会有很高的写数据库，
于是经常出现数据库链接断了。于是该系统很不稳定，经过排查发现之前这么用没出现问题，一个是因为当时
没使用run_int_executor，而是上篇说的很阻塞的使用。在那种情况下由于tornado的单,
线程机制，基本单数据库连接就够了。而如今用了很多多线程进行写后，数据连接的单例单连接,
就会出现问题了。

之后想办法改为aiomysql,但是得看工作繁忙了。。。不一定会有空去改。


