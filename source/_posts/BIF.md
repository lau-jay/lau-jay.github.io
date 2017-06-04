---
title: 内置函数与类型
date: 2016-11-25 20:23:13
categories: python
tags: BIF
---
## BIF(built-in functions) 和 type
内置函数和一些类型对与解释器来说总是可用的, py2中存在于`__builtin__`
模块中,py3的builtins模块中.

## BIF
一些常见的没什么好说的,比较有意思的和有特色的记录了下用法

- bytearray([x[)
    ```
    In: bytearray(3)
    Out: bytearray(b'\x00\x00\x00)
    In: s = bytearray(3)
    In: s[0] 
    Out: 0
    In: s[0] = 255
    In: s[0]
    Out:255
    In: s[1] = 256
    ValueError:byte must be in range(0, 255)
    In: s = bytearray(b'pyclear')
    In: s[0]
    Out: 112
    In: chr(112)
    Out: p
    In: s.decode('utf-8')
    Out: pyclear
    ```

- bytes(s, encoding)
  bytes跟bytearray很类似，只不多bytes是不可变版本的
- divmod(a,b)
  这个函数会返回一个元组,`(a//b,a%b)`,浮点数会是`(math.floor(a/b),a%b)`,complex
  不能用
- next(s,[,default])
 由于py2中是`s.next()`,py3中是`s.__next__()`
 所以为了可移植性考虑写代码的时候应该用next()

 
  

