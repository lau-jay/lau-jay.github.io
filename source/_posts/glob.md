---
title: glob
date: 2016-11-27 19:24:16
categories: python
tags: 
    - stdlib
    - glob
---
## glob module
glob通过匹配来查找文件，该模块暴露出来三个函数glob, iglob, escape

## glob
  glob通过简单的匹配规则查找文件
  例如:
  *匹配所有
  ?匹配一个字符
  ```
  import glob
  glob.glob('*') # 返回当前目录下所有的文件
  glob.glob('?.*') #返回所有文件名是一个字符的文件
  ```
## iglob
  iglob与glob其实是一回事，区别在于一个返回list一个返回iter。
  事实上通过阅读代码你会发现，glob其实就是调用iglob,然后用list函数将
  iter值取出生成list

## escape
  escapse功能比较简单，就转义掉特殊字符
 



