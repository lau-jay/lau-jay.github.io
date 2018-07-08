---
title: Ipython 笔记
date: 2018-07-08 10:06:13
categories: python
tags: Ipython
---

```
In [1]: %load_ext autoreload

In [2]: %autoreload 2

In [3]: from foo import some_function
```
之后对some_function的更改会自动reload，直接在Ipython命令行里用就是最新的了。

```
In [4]: from glob import glob

In [5]: glob?
Signature: glob(pathname, *, recursive=False)
Docstring:
Return a list of paths matching a pathname pattern.

The pattern may contain simple shell-style wildcards a la
fnmatch. However, unlike fnmatch, filenames starting with a
dot are special cases that are not matched by '*' and '?'
patterns.

If recursive is true, the pattern '**' will match any files and
zero or more directories and subdirectories.
File:      /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/glob.py
Type:      function

In [6]: glob??
Signature: glob(pathname, *, recursive=False)
Source:
def glob(pathname, *, recursive=False):
    """Return a list of paths matching a pathname pattern.

    The pattern may contain simple shell-style wildcards a la
    fnmatch. However, unlike fnmatch, filenames starting with a
    dot are special cases that are not matched by '*' and '?'
    patterns.

    If recursive is true, the pattern '**' will match any files and
    zero or more directories and subdirectories.
    """
    return list(iglob(pathname, recursive=recursive))
File:      /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/glob.py
Type:      function

```
一个问号看文档，两个问好看实现代码，不是所有的都能这么看到的，有些只能看到文档。
