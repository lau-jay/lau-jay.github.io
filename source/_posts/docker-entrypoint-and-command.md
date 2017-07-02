---
layout: post
title: "docker entrypoint"
date: 2016-08-01 21:53:38 +0800
comments: true
categories: docker
tags: pyspider docker
---
## entrypoint
今天在重新部署pyspider, 自己写了写代码利用框架，抽了个api出来，然而怎么放进docker里成了一个问题。
一开始是直接mount一个文件夹进docker，然后进入docker，将代码复制进要的目录。准备重启进程的时候尴尬的事情
发生了，docker-compose up起来的组件进程的pid是1，根本没办法kill掉再重启。

最后找到了docker-compose.yaml里command这一项，看完之后发现，都是参数。想了想去翻pyspider的Dockerfile，
于是找了作者是怎么跑的:

    ENTRYPOINT ['pyspider']

找到了怎么跑的接下来就很简单了，首先写个shell，把原本作者写的命令以及参数都写进去，在这之上加入自己
拷贝代码的shell语句。然后再在docker-compose.yaml里加上这么几行

    entrypoint:
      - bash
      - /code/replace.sh


然后再加个volume 把shell文件挂到docker的code目录就搞定了

