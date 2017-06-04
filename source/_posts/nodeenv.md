---
title: node tool
date: 2017-01-20 15:31:49
categories: node
tags: node tool
---
## install Node.js on Ubuntu server
Ubuntu的源要是不改的话，直接apt-get install nodejs, node版本会
比较低，所以要用比较新的node的话可以这样:

    curl -sL https://deb.nodesource.com/setup_5.x | sudo -E bash -
    数字替换为要安装的node版本

之后再:

    sudo apt-get  install -y nodejs

这样就能安装你指定的版本的node，其实最好的方式是nvm管理node版本或者docker。

## nvm
由于node的更新是非常快的，版本也多，有时候你用的可能非常多版本, 所以nvm基本上开发不可少的。

    nvm use version
    nvm install version
    nvm alias default version # 其实其他感觉不需太详细列出，这个是我比较容易忘记的所以特地记录下

## mongolass
使用mongoDB的好用的库我觉得莫过于mongolass了，虽然没其他的库比如mongoose名气大，但api与非常类似，迁移毫无压力。
并且我觉得更易用。

## npm

    npm help <command>
    npm install packagename -g
    这个全局安装是将所有模块安装到一个统一的模块,都是在系统Node可执行文件bin目录的同级/lib下的node_modules
    装好的同时会加个link到/bin目录里，这样才能直接当系统命令使用
    npm install packagename --registry=http://mirror.url 临时使用
    npm install <tarball file> //同本地包条件
    npm install <tarball url>
    npm install <folder> //本地包目录，必须包含package.json
    npm config set registry http://mirror.url 一直使用



## supervisor
开发时监控文件改变自动重启服务
    supervisor --harmony app.js

## nodemon
这个也是开发时监控文件改变自动重启服务,只是更容易使用，更轻量

    nodemon app.js

## 部署

对于node程序来说，一旦遇到无法处理的异常，整个进程都会挂掉，而node是单进程的。
所以在部署生产环境的时候需要使用pm2或者forever这样的进程管理工具。会帮我们进程重启。

由于我的项目中使用的是pm2, 我就只说下，docker pm2部署express的简单方式。
首先下载keymetrics/pm2-docker-alpine镜像, 剩下的步骤其实很简单就一句shell命令:

    docker run -it \
        --rm \
        --name $NAME \
        -v $(pwd):/app \
        -v $(pwd)/config:/your-config-dir \
        -p $PORT_OUT:$PORT_IN \
        -p $SSL_PORT_OUT:$SSL_PORT_IN \
        --link $DB_NAME:db \
        keymetrics/pm2-docker-alpine \
        /bin/sh -c "npm install && pm2-docker start --auto-exit --env ${NODE_ENV} process.yml"

这是主要的一句，里面的一些常量需要根据具体的情况去做赋值

