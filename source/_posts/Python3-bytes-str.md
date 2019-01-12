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
    >>>'€20'.encode('utf-8') # str to bytes
    b'\xe2\x82\xac20'
    >>> b'\xe2\x82\xac20'.decode('utf-8') # bytes to str
    '€20'
    ```

```
def to_str(bytes_or_str):
    if isinstance(bytes_or_str, bytes):
        value = bytes_or_str.decode('utf-8')
    else:
        value = bytes_or_str
    return value # is str

def to_bytes(bytes_or_str):
    if isinstance(bytes_or_str, str):
        value = bytes_or_str.encode('utf-8')
    else:
        value = bytes_or_str
    return value # is bytes
```
字符是与特定二进制无关的抽象实体，每个字符几个字节保存在抽象上看无关紧要，只有编码解码的时候
才需要关注，延伸到Python2常见的编码问题，其实本质就是一句话。以什么编码就以什么解码-by[阿驹](http://aju.space/2015/11/10/Python-character-encoding-explained.html)


说到unicode,想起很多人其实是搞不清楚unicode,和UTF-8,UTF-16等这些的关系，多说句。其实很简单，unicode是一个字符集，UTF是一个编码方案或者说具体实现。
Unicode的字符集和字符编码方案是分离的，每个字符在Unicode字符集中能找到唯一确定的编号，但是决定最终字节流，也就是二进制层面如何存放和解析的是具体的字符编码。
这实现有好几种，于是就在UTF后加上编码单位，比如8就是使用8比特为单位来编码 对同样的Unicode字符`A`，UTF8的字节流是0x41，而UTF16大端模式下得到的是0x00 0x41。

Python3的bytes/str类型让网络相关的底层库比较以迁移到Python3,我以前只知道这件事，但是没有很深刻的认知。
最近将一段Python2代码改为Python3，浏览器收到的字符永远是缺少的。
仔细查看后发觉问题出在content-length这个header字段上，bytes后的的字符串会比原字符串长。
而我传入的是原长度，所以浏览器解析的是原长度，于是就显得比原来的少。

[Python character encoding explained](http://aju.space/2015/11/10/Python-character-encoding-explained.html)
[关于编码，你需要知道的](http://www.imkevinyang.com/2010/06/%E5%85%B3%E4%BA%8E%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81%EF%BC%8C%E4%BD%A0%E6%89%80%E9%9C%80%E8%A6%81%E7%9F%A5%E9%81%93%E7%9A%84.html)
