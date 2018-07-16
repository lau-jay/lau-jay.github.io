---
title: coroutine简明介绍
date: 2018-07-15 18:20:13
categories: python
tags: 
    - Fluent
    - coroutine
---

Fluent Python阅读笔记
---

## 从可迭代对象说起
### 迭代器
使用iter内置函数可以获取迭代器的对象，如果对象实现了`__iter__`，
而`__iter__`一般返回迭代器，那么对象就是可迭代的。
序列都可以迭代，实现了`__getitem__`方法，并且参数从零开始索引的也可以迭代。

可迭代对象和迭代器之间的关系是: Python从可迭代的对象中获取迭代器。
迭代器是实现了无参数`__next__`方法的对象，使用next内置方法能返回对象中下个元素
并实现了`__iter__`，而`__iter__`一般返回实例本身，这样可以将迭代器本身当可迭代对象用。
可迭代对象一定不能是自身的迭代器，也就是不能实现`__next__`方法(反模式)。

所以过程是这样滴：
假设有个s = Sentence("Pig and Pepper")
it = iter(s)
iter -> 调用了`__iter__`于是iter返回了s本身
next -> 调用了`__next__`于是返回序列中下一个
```
>>> s = Sentence('Pig and Pepper')
>>> it = iter(s)
>>> next(it)
     'Pig'
>>> next(it)
'and'
>>> next(it)
'Pepper'
>>> next(it)
Traceback (most recent call last):
    ...
StopIteration
>>> list(it) #
[]
>>> list(iter(s)) #  ['Pig', 'and', 'Pepper']
```

### yield与生成器

Python函数的定义体中有yield关键字，该函数就是生成器函数， 生成器函数是生成器工厂，但生成器工厂不是只有包含yield的才是，只要返回一个生成器就是生成器工厂。

生成器是迭代器，会生成传给yield 关键字的表达式的值。
因为生成器是迭代器，所以调用next 会获取yield 生成的下一个元素。

在Python 3.3 之前，如果生成器函数中的return 语句有返回值，那么会报错。
现在可以这么做，不过return 语句仍会导致 StopIteration 异常抛出。并且调用方可以从异常中获取返回值，这点在把生成器当协程用的时候有意义。

生成器有个语法糖叫生成器表达式：(for i in list)，这种由括号括起来的是创建生成器的简洁句法。

### 生成器插上send就变协程鸟

大概是引入yield的五年后PEP342为生成器增加了额外的方法和功能，主要是`.send()` 与`.__next__()` 方法一样，`.send()` 方法致使生成器前进到下一个 yield 语句。
不 过，`.send()` 方法还允许使用生成器的客户把数据发给自己，即不管传给 `.send()` 方法什么参数，那个参数都会成为生成器函数。
定义体中对应的yield 表达式的值。也就是说，`.send()` 方法允许在客户代码和生成器之间双向交换数据。而 .__next__() 方法只允许客户从生成器中获取数据。

PEP 342 还添加了`.throw(...)` 和 `.close()` 方法:
前者的作用是让调用方抛出异常，在生成器中处理;后者的作用是终止生成器。

## 协程示例
```
In [2]: def simple_coroutine():
   ...:     print('-> coroutine startd')
   ...:     x = yield  # 只从调用端接收数据
   ...:     print('-> coroutine received:', x)
   ...:

In [3]: my_coro = simple_coroutine()

In [4]: my_coro
Out[4]: <generator object simple_coroutine at 0x1079f0308>

In [5]: next(my_coro)
-> coroutine startd

In [6]: my_coro.send(42)  # 发送数据给协程
-> coroutine received: 42 # 打印最后一句，函数执行定义体的末尾，抛出StopIteration
---------------------------------------------------------------------------
StopIteration                             Traceback (most recent call last)
<ipython-input-13-7c96f97a77cb> in <module>()
----> 1 my_coro.send(42)

StopIteration:

```
这个是基于生成器的协程，定义体中包含yield
协程处于四个状态:
    'GEN_CREATED': 等待开始执行
    'GEN_RUNNING': 解释器正在执行
    'GEN_SUSPENDED': 在yield表达式处暂停
    'GEN_CLOSED': 执行结束

始终要调用 next(my_coro) 激活协程——也可以调用 my_coro.send(None)，效果一样。
如果没激活就发送None之外的值会报错:
```
   Traceback (most recent call last):
           File "<stdin>", line 1, in <module>
         TypeError: can't send non-None value to a just-started generator
```

## yield from

yield from :
• 子生成器产出的值都直接传给委派生成器的调用方(即客户端代码)。
• 使用 send() 方法发给委派生成器的值都直接传给子生成器。如果发送的值是 None，那么会调用子生成器的 `__next__()` 方法。
如果发送的值不是 None，那么会调用子生成器的send() 方法。如果调用的方法抛出 StopIteration 异常，那么委派生成器恢复运行。
任何其他异常都会向上冒泡，传给委派生成器。
• 生成器退出时，生成器(或子生成器)中的 return expr 表达式会触发 StopIteration(expr)
异常抛出。
• yield from 表达式的值是子生成器终止时传给 StopIteration 异常的第一个参数。


