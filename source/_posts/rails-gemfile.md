---
layout: post
title: "Rails Gemfile 写法"
date: 2016-07-10 18:30:08 +0800
comments: true
categories: Rails
tgas: tool
---
## '~>tag'
`~>2.0.3`与`~>2.1`这两种应该是很多看的比较熟悉的Gemfile的写法，前者表示当前版本大于等于2.0.3，
小于2.1，后者表示当前版本大于等于2.1，小于3.0。

## '>='
这个是不推荐使用的，因为x.y.z约定的是x变动api不向下兼容，y是新功能，z是修复bug。
出于开发的稳定性，这个最好不要用。
所以我就很好奇。。uglifier是出于什么考虑用这个的。

## 指定版本和源
此外还可以指定版本和source源
`gem 'my_gem','2.x`, :source => 'https://gems.example.com'

## 使用放在github的Gem
如果使用放在github的源那么你可以这么写
`gem 'nokogiri', :git => 'https://github.com/tenderlove/nokogiri.git' :branch => '1.4'`
使用:branch可以精确到分支。

## 从本地安装自己的库
`gem 'my_gem', :path => './Users/lib/my_gem'`
