---
title: 理解Python的切片表示
date: 2019-08-30 10:20:13
categories: python
tags: python3, translaction
---
它非常简单:
```python
# 译者：切片用数学的区间表示的说法都是左开右闭的 [),即含左不含右
a[start:stop] # 取从start到stop-1
a[start:] # 取start到a的最后一个元素
a[:stop] # 取第0个到stop-1个元素
a[:] # 复制整个list
```
还有个step(步长)的值,可以一起用:
```python
a[start:stop:step] # 从start开始包含start，到stop-1为止，取每隔step个的元素
# 译者注:
a = [1,2,3,4,5]
a[0:4:2] == a[1,3]
```
要记住的关键点是`:stop`值表示不在所选中的元素中的第一值,所以stop和start他们之差
是选择的元素的数量(如果step是默认值1)

另一个特征是start或stop可能是负数，这意味着它从list的末尾而不是开头开始计数。所以:
```python
a[-1] # 最后一个元素
a[-2:] # 最后两个元素
a[:-2] # 去除最后两个元素的数组
# 译者注: 其实就是 a[:len(a) + (-2)]
a = [1,2,3,4,5]
a[:-2] == [1,2,3]
```
同样，step可能是负数：
```python
a[::-1]    # 复制整个数组，以倒序复制
a[1::-1]   # 复制从index为1往前的到开头，倒序复制
a[:-3:-1]  # 复制从-3开始结尾的元素, 倒序复制
a[-3::-1]  # 复制从-3到开头的元素，倒序复制
```
Python对程序员很友好，如果你list越界了。例如，如果取a[:-2]但是a只有一个元素，你会得到
一个空的list而不是一个错误。有时候你更喜欢错误，而不是这种隐晦的结果，那么你必须要知道
会有这种情况。

### 与slice对象的关系
切片运算符 `[]` 实际上在上面的代码中通过 `:` 使用了一个slice()对象(只在[]内有效)，
例如：
```python
a[start:stop:step]
```
等价于
```python
a[slice(start, stop, step)]
```
Slice对象根据不同的参数会有不同的行为,跟range非常类似，比如 `slice(stop)` 和
 `slice(start, stop, step)` (note: 原文有误，修改了下)。如果要跳过给定的参数，可以使用
  `None`, 例如 `a[start:]` 等价于 `a[slice(start, None)]` 或者 `a[::1]` 等价于
  `a[slice(None, None, -1)]`

虽然基于 `:` 符号的对于简单切片非常有用，但是显式使用slice()对象简化了使用切片时自动生成slice()对象。


# 原文
[understanding-slice-notaion](https://stackoverflow.com/questions/509211/understanding-slice-notation)


