---
title: Nginx代理CSS失效
date: 2017-10-10 20:23:13
categories: Nginx
tags: 
    - web 
    - tools
---
# Nginx部署Vue
写好Nginx的conf能够正常显示静态页面了，结果css全乱了。由于js
能够正常使用，我以为跟我conf没关系。
最后实在找不到原因，就找了一个Vue写的溜的朋友看了下。
他找到原因了:
    `Content-Type: application/octet-stream`
我一看绝对是我的锅，可是比对conf,include mime.types有加啊!
可是这个Content-Type绝对是mime有问题，随手cat了下这个mime.types，
然后我恍然大悟，丫空的。将默认的那个mime.types内容cat进，CSS就正常了。






