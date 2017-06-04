---
layout: post
title: "docker 基本使用"
date: 2016-07-12 09:13:45 +0800
comments: true
categories: docker
---
## docker container
### 如何进入运行起来的docker container
如果想要进去运行起来的container里看看日志什么的，那么使用docker 1.3
之后添加的exec

    docker exec -i -t CONTAINER_ID bash


### 查看log

    docker log -f CONTAINTER_ID


