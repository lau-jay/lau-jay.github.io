---
title: flask-mail
date: 2016-12-11 12:22:16
categories: python
tags: 
    - python 
    - flask
---
[原文出处](http://blog.csdn.net/stan_pcf/article/details/51098126)
没事捡起flask写点小玩意，在设置邮件的时候有点麻烦了，国内的话用163或者qq会比较方便。至少延时会比较低，但是配置不太一样，这点之前在用OSX自带的邮件客户端时候就遇到了。
首先你得登陆163邮箱开通POP/SMTP/IMAP,(设置－> POP3/SMTP/IMAP -> 客户端授权码),说到这里我就
得吐槽下，为毛邮箱大师客户端没mac的。获取到的授权码才是在第三方用的密码。
接着在其他部分按照文档上的来，只是把相应的改成网易的就好了,最后实际发个邮件测试下就行。

