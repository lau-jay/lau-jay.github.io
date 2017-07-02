---
layout: post
title: "docker 基本使用"
date: 2016-07-12 09:13:45 +0800
comments: true
categories: docker
---
## docker使用笔记
随用随记，并不成系统，主要是方便自己忘记的时候查阅。

### 如何进入运行起来的docker container
如果想要进去运行起来的container里看看日志什么的，那么使用docker 1.3
之后添加的exec

    docker exec -i -t CONTAINER_ID bash

-i 其实是STDIN，也就是使用标准输入，这样你写的指令就能在容器里执行。
-t 其实是tty的意思，就是使用终端，这样可以有个终端可以随时输入命令操作。

### 查看log

    docker log -f CONTAINTER_ID

### `.`,`./`,以及绝对路径
写习惯了docker-compose.yaml就会出现一个问题: -v 给的路径写的时候
常常会直接用相对路径，这在用yaml作为配置，用docker-compose编排叫起来
docker组的时候是没有问题的。

但是直接用docker命令叫起单个container的时候那个值可是直接就给docker的，
并没有人解析补全路径，所以往往会报错。

所以之后养成的习惯就是直接用`$(pwd)`这样可以避免出错，有时候忘了这茬还是会
浪费点时间去看文档的。






