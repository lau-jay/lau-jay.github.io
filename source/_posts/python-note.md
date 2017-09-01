---
layout: post
title: "Python note"
date: 2016-07-12 16:41:52 +0800
comments: true
categories: python
---
## 负偏移
字符串的负索引访问，其实可以这么看，就是访问字符串长度加上负数索引得到的index所指向的字符。
例如如果line这个字符串是'hello pyclear'，那么下面两行的执行结果是一样的。

    line[-1:]
    line[len(line) + (-1):len(line)]`


嵌套+列表解析的好用

    [{'title: i[0]，'url': i[1]} for i in data]

## round()坑
round()内置函数会将传入的数值四舍五入，坑的地方在于

    Python2: In [1]: round(1.5)
             Out[1]: 2.0
             In[2]: round(0.5)
             Out[2]: 1.0
             In[3]: round(-0.5)
             Out[3]: -1.0

在2中会四舍五入到最近的倍数，而且类型不变。
然而3中是这样的

    Python3: In [1]: round(1.5)
             Out[1]: 2
             In [2]: round(0.5)
             Out[2]: 0

在3中会将距离两个倍数值相等的只舍入最近的偶数倍数，并且值是整数。
<!--more-->

## reload
reload在Python2中是一个BIF但是到了Python3中，
Python3删除了reload内置函数，将其放入imp标准库中了

## chr
参数的有效范围为0到1,114,111（基址16中的0x10FFFF）。如果i超出该范围，则会引发ValueError。

## 异常
突然发现一个司空见惯的东西其实不是那么简单，这就是异常。
老的代码经常是这么写except语句的

    except ExcType，var

这个在2.7之前包括2.7.10中都是有效的，但事实上是已经被废弃的语法。只是为了兼容
考虑所以还有效。取而代之的是使用as var语法，2.7.x 和3.x(注意3.x只能这么写)
都是。

    except Exctype as var

对于异常来说，有一整套的东西，try，except，finally。

    def test():
        try:
            print('to do')
            print('to return in try')
            return 'try'
        except Exception as e:
            print('process except')
            print('to return in except')
            return 'except'
        else:
            print('run normal')
        finally:
            print('to return in finally')
            return 'finally'
        return 'test'

    value = test()

    to do
    to return in try
    to return in finally

    print(value)
    'finally'


这个函数运行起来后会体现异常捕捉的一些特性，finally是无论产生异常与否都会
执行的代码，特性之一是，无论是否产生异常，当try或者except里执行到return的时候
都会中断转向finally代码块。所以return是重点，而return之后是什么其实由于中断都
不会返回的。如果try里没return导致中断的话，那么else里的就会被执行。

## zip
在Python2中zip是很挫的完全使用两个序列创建一个元组的列表，如果两个序列的
长度一大，那么效率上是很成问题的。itertools.izip()效果与zip类似，但是每次只
生成一个元组而不是创建个很大的元组列表，Python3的zip类似与izip()的方式。
欢迎使用Python3。

当zip产生的是迭代器的时候, 它的内容只能被消费一次，所以

zip_number = zip([i for i in range(10)], [j for j in range(10)])
print(list(zip_number)) # [(0,0),(1,1)...]
print(list(zip_number)) # [] 空array


## with

* 从Python 2.7 和Python 3.1 起允许在with 语句中使用两个上下文管理器

## 第一类
所有能够用标识符命名的所有对象都是具有相同状态的，即能够命名的所有对象都可以当作数据处理
Python中一切都是第一类的。

    line = "GOOGLE，100，490.10"
    field_types = [str，int，float]
    raw_fields = line.split('，')
    fields = [ty(val) for ty，val in zip(field_types，raw_fields)]

## 不可变对象(mutable)做参数
由于可变对象其实是保存了引用的。当修改默认参数之后， 默认参数会保留之前
调用进行的修改。所以如果这么写，基本上就会出问题。

    def test(arg1，items=[]):
        items.append(arg1)
        return items

    In: test(1)
    Out:[1]
    In: test(2)
    Out:[1，2]
    In: test(3)
    Out:[1，2，3]


所以这么写才是好的选择:

    def test(arg1， items=None):
        if items is None:
            items = []
        items.append(arg1)
        return items


## 闭包与装饰器
所谓的装饰器是可以在原有函数基础上透明的修改或增强包装对象的行为
闭包指延伸了作用域的函数，其中包含函数定义体中引用，但是不在定义
体中定义的非全局变量... 它能访问定义体之外定义的非全局变量

将要增强或改变行为的函数作为参数传入:

    @world
    def hello(s):
        return s


    def hello(s):
        return s
    hello = world(hello)

这二者是等价的。

如果有多个那么是按照先后顺序来的, 越接近函数的越早:

    @foo
    @bar
    @spam
    def grok(x):
        pass

    def grok(x):
        pass
    grok = foo(bar(spam(grok)))

使用装饰器的方式修改函数有个问题，新生成的函数会缺少很多原函数的属性，如docstring和name

内置的functools通过update_warpper函数解决了这个问题，然而每次手工调用
这个函数不方便，所以该模块提供了个装饰器wraps

    @functools.wraps

## 生成器
g = ( n for i in range(3)) 这是一个生成器表达式
生成器可以通过支持迭代协议的对象使用,生成器具备一个可消费性的特征，
只能被迭代一次, 相当于一次性用品。

## 协程
协程可以仅仅由loop语句中的:

     n = (yield)

构成，send传给协程的值都会被传给n，从而可以向协程内传递值
也可以

     n = (yield result)
     .....
     result = ...

这里result将返回， 每次获取的值都赋值n。。result返回的将是这次的值的结果
由下一次的yield表达式返回。

## super
派生的类可以重新实现基类的方法，但是有时候想调用基类的方法.

    class SonClass(BaseClass):
        BaseClass.method(self，arg)

这样使用就算类BaseClass中没实现method， 方法但只要BaseClass的继承的类中有
那么代码可以正常运行。

因为这样自很容易造成误解，一般使用super()函数

    class SonClass(BaseClass):
        super(SonClass，self).method(arg)

super返回一个特殊的对象这个对象能在基类上执行属性查找，
这样无论是哪个基类实现的都能正常调用。

## 对象身份与类型
id、type、is(关键字) 分别能够获取内存位置，获取对象类型以及比较对象身份
类型本身是一个对象(python3.x)，该对象对某个类型的所有实例都是一样的例如：
```
   a = 'x'
   b = 'x'
   type(a) is type(b) # true
   a is b # false
```
这个可以用来检查类型，但是更适合的是isinstance（obj，type):
```
   s = []
   if isinstance(s,list):
       print('true')
```

## del
del可以减少引用计数,虽然我不怎么用它。

## copy stdlib
对一个可变对象copy标准库的deepcopy将会深复制，而不是简单的新建引用

## set()、{}、{1,2,3}
对于集合，有时候真的会跟字典混起来，set()是空集合，{} 是空字典，{1,2,3}是集合
而{'1':1,'2':2,'3':3}是字典, {'12345'} == set(['12345']) 而
set('12345') == {'1','2','3','4','5'}

