---
title: Tornado IOLoop
date: 2018-09-08 11:40:17
tags: tornado 
---
上一篇详细介绍了Reactor模型，IO多路复用作为预备知识，还简略介绍了IOLoop与IO多路复用，以及Reactor 模型的关系。
这篇我想具体描述下IOLoop类。

### IOLoop类

```
A level-triggered I/O loop.
```

我们使用epoll(Linux) 或 kqueue(BSD和Mac OS X）如果它们能用，不能用将退而使用select()。

IOLoop就是对I/O多路复用的封装，它实现了一个单例，将这个单例保存在IOLoop._instance中。

此外IOLoop还被用来集中运行回调函数以及集中处理定时任务。

IOLoop,它的父类是Configurable类，也就是说：IOLoop是一个Configurable子类
Configurable十分重要，位于tornado.util文件中,它是一个工厂类。
它要求它的子类必须具备三个方法，有点协议的意思，Python中的协议跟接口其实不严格看可以算作一回事。

* `configurable_base` 
* `configurable_default` 
* `initialize`

因为Configurable中重写了`__new__`方法，它的子类可以不用定义`__init__`，直接使用iniaialize来替代，所以才initaialize才会是其子类必须具备的方法。

