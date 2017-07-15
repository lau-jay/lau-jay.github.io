---
title: bytes
date: 2017-06-13 21:56:48
categories: python
tags: code
---
## bytes
虽然是二进制，但其实是整数序列，各个字节的值可能有三种情况:

* 可打印的ASCII的字节使用ASCII本身
* 制表符、换行等对应的字节，使用转义序列\t...
* 其他字节的值，使用十六进制转义序列（\x00)

除了格式化方法和几个处理unicode数据的方法，str类型的其他方法都支持bytes和bytearray
如果正则表达式编译自二进制序列的话，那么正则表达式函数也能处理二进制序列。

encode使用默认参数errors可以在编码出错时进行特殊处理

解码是在应用程序使用字节的时候发生的，此时需要了解字节的含义；
编码实在程序对外输出字符的过程，此时应用程序使用某一种计算机所
使用的编码方法将字符串转化为字节。

