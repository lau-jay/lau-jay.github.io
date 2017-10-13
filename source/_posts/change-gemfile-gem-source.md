---
layout: post
title: "修改Gemfile源"
date: 2016-07-16 08:02:59 +0800
comments: true
categories: Rails
tags: ruby
---
## 改rails源代码里的source

    cd "$(gem environ | gawk '/INSTALLATION DIR/{print $4}')"
    cd ./gems/railties-*/lib/rails/generators/rails/app/templates
    vim ./Gemfile
    
直接修改source
正如参考文章的作者所说，升级Rails之后会被覆盖，所以法二更合适

## 直接bundle config全局有效
`bundle config 'mirror.https://rubygems.org' 'https://ruby.taobao.org'`

但事实上，我更喜欢每次都加个参数`--skip-bundle`不自动bundle install。然后手动修改，再手动bundle install

[*参考*](http://liuxiang.logdown.com/posts/161475-sets-the-default-gem-source-ruby-in-the-gemfile-generated-at-rubytaobaoorg)
