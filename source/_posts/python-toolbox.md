---
layout: post
title: "python toolbox"
date: 2016-07-22 21:59:12 +0800
comments: true
categories: python
tag: tools
---
## 包管理工具
Python有很多非常棒的工具，pip应该是最最常用，也是最最基本，在pytoon的地位
就如同debian系的apt， CentOS的yum以及OSX的brew。

Python有个挺讨厌的地方就是项目的依赖包，Ruby有个Bundler利用Gemfile解决了这个问题
Python的pip也具备类似的功能(不过感觉没npm以及Bundler好用)。

    pip freeze > requirements.txt

这样就产生了基本在开源项目里都能见到的一个文件。
该文件包含了所有依赖的库及库的版本，命令行下直接

    pip install -r requirements.txt

## virtualenv
virtualenv是用来为一个应用创建一套"隔离"的Python运行环境
`$virtualenv --no-site-packages venv`
然后:
    source ./venv/bin/activte

启用虚拟环境，在该环境下，安装的包和python解释器都是在当前目录下，不会
改变系统环境。

    deactivate

这样就可以退出虚拟环境了
使用的时候我们会创建一个跟venv平行的目录，用来作为workdir，当使用git时候
只需要在`.gitignore`文件里添加一句`./venv`就好了

## pyvenv
在Python3.5里集成了个工具(Python3.3开始就有)，pyvenv，这个工具跟virtualenv一样，能够用于构建
虚拟环境，从而不必特地去安装virtualenv，其用法也跟virtualenv一样。

    source ./venv/bin/activte

启用虚拟环境，在该环境下，安装的包和python解释器都是在当前目录下，不会
改变系统环境。

    deactivate

这样就可以退出虚拟环境了

## python3 -m venv
pyvenv 在Python3.6里已经被弃用了,文档原文是:

```Deprecated since version 3.6: pyvenv was the recommended tool for creating virtual environments for Python 3.3 and 3.4, and is deprecated in Python 3.6.Changed in version 3.5: The use of venv is now recommended for creating virtual environments.
```




