---
title: python and db
date: 2017-07-01 10:33:07
tags: 
    - orm
    - python
---

## SQLAlchemy
使用SQLALchemy的时候按照python2的老习惯，直接把driver给省略了。
他默认会是MySQLdb，然后必然报错了，因为是python3我只装了pymysql。

看到报错后赶紧把driver写上pymysql。默认值大多数时候是好的，省事的。
但是换了个环境最好要注意下，个人觉得避免犯错的做法是不要给它使用默认
值的机会，直接写完整，有些可以偷懒有些最好不要。
