---
title: random
date: 2016-12-01 21:26:58
categories: python 
tags: stdlib
---
## random module
random模块提供了各种用于生成伪随机数的函数，其源码有这么一句话，*Adapted by Raymond
Hettinger for use with the Mersenne Twister and os.urandom()*透露了random
函数的基本原理。

## 控制随机数生成器状态
random模块暴露出的一部分函数用来控制基础随机数生成器的状态。
seed用于初始化随机数生成器，默认使用系统时间，如果传入参数，
那么依据参数来初始化，这个参数类型是有要求的，要么整数，要么
可散列的对象。
getstate() 和 setstate(state)
getstate获取到的生成器状态可用于传递给setstate恢复随机数生成器


