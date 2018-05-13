---
title: 不知道的Python3
date: 2018-05-12 22:20:13
categories: python
tags: python3
---
下面这几个是我日常基本没使用过的特性。
从这里挑的[内容来自]('https://zhuanlan.zhihu.com/p/36580468')
## 高级解包
```
# in python2
In [2]: a, b = 1, 2

In [3]: a, b = b, a

# now in python3
In [4]: a, b, *rest = range(10)

In [5]: print(a)
0

In [6]: print(b)
1

In [7]: print(rest)
[2, 3, 4, 5, 6, 7, 8, 9]

In [8]: a, *rest, b = range(10)

In [9]: print(a)
0

In [10]: print(rest)
[1, 2, 3, 4, 5, 6, 7, 8]

In [11]: print(b)
9

```

## 强制关键词参数
如果不想用*args收集多余参数你可能这么写
```
In [14]: def mysum(a, b, biteme=False):
    ...:     if biteme:
    ...:         print('一键删库')
    ...:     else:
    ...:         return a + b
    ...:

In [15]: mysum(1,2)
Out[15]: 3

In [16]: mysum(1,2,3) # if this is delete DB?
一键删库

In [18]: def mysum(a, b, *, biteme=False):
    ...:     if biteme:
    ...:         print('一键删库')
    ...:     else:
    ...:         return a + b
    ...:

In [19]: mysum(1,2,3) # now raise TypeError
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-19-eb8c192f30fb> in <module>()
----> 1 mysum(1,2,3)

TypeError: mysum() takes 2 positional arguments but 3 were given

```

## 新的标准库
```

In [22]: import ipaddress

In [23]: print(ipaddress.ip_address('192.168.0.1'))
    ...: print(ipaddress.ip_address('2001:db8::'))
    ...:
192.168.0.1
2001:db8::

In [24]: from functools import lru_cache
    ...: from urllib.error import HTTPError
    ...: import urllib.request
    ...:

In [25]: @lru_cache(maxsize=32)
    ...: def get_pep(num):
    ...:     'Retrieve text of a Python Enhancement Proposal'
    ...:     resource = 'http://www.python.org/dev/peps/pep-%04d/' % num
    ...:     try:
    ...:         with urllib.request.urlopen(resource) as s:
    ...:             return s.read()
    ...:     except HTTPError:
    ...:         return 'Not Found'
    ...:

```

## pahtlib
```
from pathlib import Path

directory = Path("/etc")
filepath = directory / "hosts"

if filepath.exists():
    print('hosts exist')

```
这个比通常用os库做的优雅多了。。特别是filepath这句特别黑魔法
