---
title: collections 
date: 2016-11-29 23:35:32
categories: python
tages: stdlib
---

## collections module
collections提供了些新类型：deque和defaultdict, namedtuple等。
## deque
deque是性能非常高的，插入和删除优化到接近O(1),并且具有很有意思的特性。
```
   from collections import deque
   dq = deque([1,2],12)
   for i in range(3,15):
       dq.append(i) #添加到右端
   id(dq[0]) #4297546624
   id(dq[1]) #4297546656
   dq.append(18)
   id(dq[0]) #4297546656
```
用id来看添加值前后的内存地址会发现现的一个有意思的地方, 如果限制了大小，当满了以后那么添加新项目的话，会在相反的一端删除对象，添加了一个值后dq[0]的id变成了原来dq[1]的id值, 原来dq[0]里面的内容也被删除了, 这样的实现无疑保证了性能。

deque是双端队列，在插入的时侯性能极好并且deque是线程安全的，appendleft和append是用来分别在左端和右端添加数据。

还有个基于双端数据结构的函数：rotate, 它可以轻易的旋转所有项，rotate的参数
值大小决定旋转步数，正负决定旋转方向, 正向右转，负向左转。

## defaultdict
与字典基本一样，除了对不存在的键将调用default_factory提供的函数来提供默认值，实际上与字典get取值设置默认值类似，只不过换成key不存在设置该key的值，并将这个key与所调用函数生成的值保存起来.


## namedtuple
```
   from collections import namedtuple
   NetAddress = nametuple('NetAddress', ['hostname','port'])
   a = NetAddress('localhost',80)
   a.hostname # 'localhost'
   a.port # 80
   type(a) # ipython output __main__.NetAddress 
   isinstance(a, tuple) # True
```
其实是构造了一个tuple的子类, 可以使用各种tuple的方法，但却提供了更方便的数据结构，isinstance验证了这点。
namedtuple具备更小的内存消耗，但是.方式的属性访问没类快。
## 源码
有个点得记下，看collections的源码的时候发现，其使用了个_collections模块, 在标准库根本找不到，后来在爆站找到答案，居然是个内置库，c写的。

