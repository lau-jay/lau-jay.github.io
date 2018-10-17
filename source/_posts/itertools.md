---
title: 不知道的生成器
date: 2018-07-09 22:20:13
categories: python
tags: python3
---
标准库提供了很多生成器，还有些是内置的比如filter(python3里)
按照fluent python书里的表14-x系列，列出了我日常很少用的函数。
note: 有个库叫做more-itertool, 这里的其实比标准库内的更常用

## 用于过滤的生成器函数
itertools.dropwhile(predicate, it) 处理it，跳过predicate的计算结果为真的元素，
然后产出剩下的各个元素，不再进一步检查。

itertools.takewhile(predicate, it) predicate返回真值时产出对应的元素，然后立即停止，不再继续检查。
这个少用的原因是，这个其实是部分检查，我日常处理的一般是需要全部的。

## 用于映射的生成器函数
itertools.accumulate(it, [func]) 产出累积的总和;如果提供了 func，那么把前两个元素传 给它，然后把计算结果和下一个元素传给它，以此类推， 最后产出结果.



