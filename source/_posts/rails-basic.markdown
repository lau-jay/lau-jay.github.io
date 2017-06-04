---
layout: post
title: "rails basic"
date: 2016-07-16 08:24:24 +0800
comments: true
categories: rails
---
## 生成app

`rails new toy_app` 生成应用的骨架

## 引入的包管理

Gemfile 存放了所有模块，该文件内，通过

    group flag do
      module
    end

来划分不同的env下需要的lib，flag指定为:development，:test
然后将production 放在:production group下
这样
`bundle  install --without production`在安装时使用参数不安装production内的lib

## 控制器的生成

`rails generate controller StaticPages home help`
`rails destroy controller StaticPages home help`
这二者是抵消的，一个生成一个撤销

## 数据库的迁移和撤销

`bundle exec rake db:migrate`
`bundle exec rake db:rollback`
这二者用于数据库迁移和撤销迁移

指定迁移文件迁移
`bundle exec rake db:migrate VERSION=0`
数字是生成的迁移顺序号。
