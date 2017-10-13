---
layout: post
title: "docker部署pyspider"
date: 2016-07-18 11:37:52 +0800
comments: true
categories: docker
tags: tools,python
---
## 缘起
最近在搞docker部署pyspider，pyspider是一个非常灵活用户友好的爬虫框架，基于该框架能够很容易写出可控的爬虫。
由于对docker不够熟悉，花了不少时间在解决部署问题上，很多地方纠结了蛮久的。写点笔记纪录使用经历。
当然我最后还是没单机docker部署pyspider。。不过单机也用不着组件化。。

## 首先，将redis和postgresql启动

    # docker run --name postgres -v /data/postgres/:/var/lib/postgresql/data \ 
    -d -p $LOCAL_IP:5432:5432 -e POSTGRES_PASSWORD="" postgres
    # docker run --name redis -d -p  $LOCAL_IP:6379:6379 redis

## 其次，创建数据库用户和数据库
在实际部署中有个坑是这样的, docker-compose.yaml里db的用户名是由name@hostip里的name决定的，如果你没有创建name相应的role那么fetcher和processor会报错。
创建完了role之后接着需要创建三个数据库taskdb,resultdb,projectdb。
不会创建？

    # docker exec -it postgres bash

然后在打开的容器shell里输入

    $ psql -U postgres

<!--more-->
好剩下的增加root用户以及添加root所有的三个数据的postgresql
操作可以看阮一峰的postgresql入门教程

## 接着，将调度器启动

    # docker run --name scheduler -d -p $LOCAL_IP:23333:23333 --restart=always binux/pyspider \
    --taskdb "sqlalchemy+postgresql+taskdb://binux@10.21.0.7/taskdb" \
    --resultdb "sqlalchemy+postgresql+resultdb://binux@10.21.0.7/resultdb" \
    --projectdb "sqlalchemy+postgresql+projectdb://binux@10.21.0.7/projectdb" \
    --message-queue "redis://10.21.0.7:6379" \
  scheduler --inqueue-limit 5000 --delete-time 43200

  这里的db的ip需要注意。我实际使用发现需要先`docker exec -it postgres cat /etc/hosts` 看下容器内的ip
  然后写pgsql的ip，使用宿主机ip会有点问题。等我对docker更熟悉了估计能找出问题的解答。
 
  还有个问题需要注意，调度器的--message-quque的地址一定不能写错，如果写错那么就会出现单步你调试可以，但是一运行就会
  出现unknown project的情况。

    [E 160719 12:30:56 scheduler:238] unknown project: testdocker
    [I 160719 12:31:35 scheduler:505] in 5m: new:0,success:0,retry:0,failed:0
    [I 160719 12:32:35 scheduler:505] in 5m: new:0,success:0,retry:0,failed:0
    [I 160719 12:33:35 scheduler:505] in 5m: new:0,success:0,retry:0,failed:0
    [E 160719 12:33:44 scheduler:238] unknown project: test2
    [I 160719 12:34:35 scheduler:505] in 5m: new:0,success:0,retry:0,failed:0
 
## 启动其它组件
 在另外一台服务器使用docker-compose将其它组件启动启动
 `# docker-compose up`

compose.yaml的配置参照pyspider作者的[部署博文](http://blog.binux.me/2016/05/deployment-of-demopyspiderorg/)有删改
我依据实际情况去掉了webui的负载均衡，以及改ip，改动不大，注意webui
去掉负载均衡后需要自己将端口expose出来。
