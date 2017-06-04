---
title: runtime module
tags:
---
# Runtime
## atexit

## copy
Python 解释器的运行时中对象管理是一个重要的部分，copy模块包含创建对象的深浅拷贝的函数
- copy(x):浅拷贝,拷贝引用，对内置的类型不是很常用，因为有对应的内置函数如tuple,list,dict
- deepcopy(x,[,visit]):这个就不仅仅是拷贝引用了，是将x对象的所有值等复制的深度复制，可以说这才是真正意义
的拷贝,通过__copy__和__deepcopy__可以实现类自定的复制方法。

## inspect
- cleandoc(doc):将文档字符串doc中所有tab改为空格，并删除为了对齐而插入的缩进，没觉得用处在哪。。
- currentframe(): 这个能返回程序调用中的栈帧对象。
- getargspec(func):这个能返回一个函数的参数的数组ArgSpec(args, varags, varkw, defaults)
- formatargspec(args [, varage [, varkw [,defaults]]]):把上面函数的返回值一样但格式良好的字符串



