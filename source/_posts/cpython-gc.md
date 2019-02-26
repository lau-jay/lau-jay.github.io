---
title: cpython-gc
date: 2017-08-21 13:40:17
tags: python
---
note: 以下讨论的都是CPython
## 内置数据结构
Python的dict，tuple等内置数据结构的定义里包含里与GC有关的成员，Python
的垃圾回收采用的是引用计数的方式，也就是说有保持计数器的成员。

研究所有内置的数据结构会发现所有的内置类型的结构体都在开头保留里PyObject结构体
(CPython源码Include目录下的object结尾的头文件)
```c
106 typedef struct _object {
107   _PyObject_HEAD_EXTRA
108   Py_ssize_t ob_refcnt;
109   struct _typeobject *ob_type;
110 } PyObject;
```
而Pyobject结构体的ob_refcnt成员负责维持引用计数。

## 内存分配器

PyDict_New()      -> 3 (level)
PyObject_GC_New() -> 2
PyObject_Malloc() -> 2
new_arena()       -> 1
malloc()          -> 0

Python中并不是在成成所有对象时都调用malloc(), 而是根据要分配的内存大小来改变分配的方法。
大于256bytes,使用malloc,小于该值，1 和2层的就被调用。

小于256bytes的会利用第一层的内存分配器，这一层的分配器会事先从
0层开始迅速保存内存空间，就是不还给操作系统，一直申请着方便内部小对象复用

第一层的内存结构名与大小关系如下：

    arena > pool > block

arena 内部各个 pool 的大小固定在 4K 字节。因为几乎对所有 OS 而言，其虚拟内存的页
面大小都是 4K 字节。
大多数 OS 都是以页面为单位来管理内存的。把页面大小和 pool 大小设定成相同的值,
我们就能让 OS 以 pool 为单位来管理内存。
为了避免频繁调用 malloc() 和 free()，第 0 层的分配器会以最大的单位 arena 来保留内存

