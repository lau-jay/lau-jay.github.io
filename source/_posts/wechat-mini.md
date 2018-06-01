---
title: 微信小程序坑点: 40029,invalid code
date: 2018-06-01 13:20:13
categories: python
tags: issue
---
前天为了获取session_key和openid，后端拿到code之后无论怎么请求都是invalid code。
从appid到各种配置，乃至调用次数全查了个遍没查到为啥。
从各种渠道搜到的都是说应该是appid或者secret错误，但是很确认无误了。

昨天又查了下，还是没辙于是暂时绕过先做自己的用户登录验证了。
结果做到小程序支付后需要opneid，看来是绕不过去了，于是直面刚这个问题了。

于是又去翻文档，各种谷歌，突然看到csdn有个人说了大坑之一是，如果修改了appid，project.config 那里的
改了没用，要用改了后的appid新建个项目。。。我让前端试了下。。wtf。。

坑爹啊坑爹，想起java-tools GitHub的issue里有个人也是，过了一天啥代码都没改就好了。应该也是类似问题。

问题解法[原文](https://blog.csdn.net/u010520626/article/details/80366688)
