---
title: Python3 bytes/str
date: 2016-11-27 15:52:34
categories: python
tags: python
---
最近看到篇讲解Python3的str和bytes区别的好[文章](http://eli.thegreenplace.net/2012/01/30/the-bytesstr-dichotomy-in-python-3)
Python3文本总是Unicode,用str表示，二进制数据由bytes类型表示。
里面有句话是精髓所在: *The encoding is a crucial part of this translation process*
编码是核心所在:
    ```
    >>>'€20'.encode('utf-8')
    b'\xe2\x82\xac20'
    >>> b'\xe2\x82\xac20'.decode('utf-8')
    '€20'
    ```
字符是与特定二进制无关的抽象实体，每个字符几个字节保存在抽象上看无关紧要，只有编码解码的时候
才需要关注，延伸到Python2常见的编码问题，其实本质就是一句话。以什么编码就以什么解码-by[阿驹](http://aju.space/2015/11/10/Python-character-encoding-explained.html)


说到unicode,想起很多人其实是搞不清楚unicode,和UTF-8,UTF-16等这些的关系，多说句。其实很简单，unicode是一个字符集，UTF是一个编码方案或者说具体实现。这实现有好几种，于是就在UTF后加上编码单位，比如8就是使用8比特为单位来编码。关于编码更多更具体的可以看[这](http://aju.space/2015/11/10/Python-character-encoding-explained.html)

Python3的bytes/str类型让网络相关的底层库比较以迁移到Python3,我以前只知道这件事，但是没有很深刻的认知。
最近将一段Python2代码改为Python3，浏览器收到的字符永远是缺少的。
仔细查看后发觉问题出在content-length这个header字段上，bytes后的的字符串会比原字符串长。
而我传入的是原长度，所以浏览器解析的是原长度，于是就显得比原来的少。
