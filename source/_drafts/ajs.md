---
title: another-json-schema阅读
date: 2017-03-09 22:37:36
categories:
tags: node
---
这个node包用于可以方便的使用schema，可以为mongondb数据及其他需要验证数据格式的
地方。
其支持了gt,get,lt,lte,enum,还有正则这几种额外的辅助方式
helper.js:

   直接采用Object.prototype.toString.call(value)
   来判断数据类型。


index.js:

    AJS
