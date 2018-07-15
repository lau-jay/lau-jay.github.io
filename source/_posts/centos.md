---
title: CentOS
date: 2017-09-11 20:23:13
categories: Linux
tags: 
    - centos
    - python
---
CentOS升级Python 之后，由于将默认的python 指向了python3，yum不能正常使用，
需要编辑yum 的配置文件。

`sudo vim /usr/bin/yum`

`sudo vim /usr/libexec/urlgrabber-ext-down`

 #!/usr/bin/python 改为 #!/usr/bin/python2.7


