---
title: functools
date: 2016-12-03 20:49:16
categories: python
tags: 
    - stdlib 
    - python
---
## functools module
functools 模块包含了非常多很有用的函数，甚至可以说是比较常用的。这些函数主要用于创建高阶函数、函数式编程和装饰器的函数和装饰器。

## partial()
```
   def spam(a,b,c,d):
       print(a,b,c,d)
   from functools import partial
   s1 = partial(spam,1)
   s1(2,3,4) # 1,2,3,4
```
在类定义中使用partial, 那么这个函数的行为类似于静态方法而不是实例方法。

## reduce(function, items [,initial])
```
   def add(a,b):
       return a+b
   from functools import reduce
   reduce(add, [i for i in range(1,11)]) # 55
```
从可迭代对象中取前两个值，交给作为第一个参数传入的处理函数处理(类似map的第一个参数)，完了再将函数的返回值与
可迭代对象中的下一个值作为处理函数的值。从这可以知道这个处理函数必须要能接受两个参数。
## update_warpper
我在之前的python-note中记录过一点，简单来说就是为了能够保有原始函数的包括docstring在内的一些属性而提供的。然而每次调用update_warpper不方便, 于是就有了下一小节的warps。
##  warps
```
from functools import wraps
def corator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print('calling decorated function ..')
    return wrapper

@corator
def test():
    """
    docstring
    """
    print('called test function')
print(test.__name__,test.__doc__) # test docstring
```
与update_warpper功能类似，但身为装饰器更容易使用。



