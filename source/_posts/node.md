---
title: node_note
date: 2017-01-20 15:31:49
categories: node
tags: node
---
# 本文是深入浅出Node.js及其他一些书籍的阅读笔记.
## 预定义变量exports、 module

    //node 模块化是标准思想法，一个文件就是一个模块, 这点很值得吐槽, 不是说不好，
    //只是会非常多的包往往就几行代码
    var foo = require('./foo');
    var foo1 = require('./foo.js');

    // 模块导出对象 类似python的__all__指明被导入该模块的可见的对像
    exports.hello = function(){
        console.log('Hello World');
    }

    //module 包含该模块的一些信息，还可以用来修改模块导出对象
    module.exports = function() {
        console.log('Hello World!');
    }

## require导入规则
 
    如果加路径，不论绝对相对，都是依据路径所指的，如果不使用路径是依据：
    内置模块->node_modules -> NODE_PATH
    var fs = require('fs') //内置模块只要模块名就好了
    把这行代码: console.log(module.paths)放进一个文件里执行,可以看到输出一个数组，该数组
    的第0个值是先当前目录下的node_modules目录,然后第一个是父目录的node_modules目录...
    最后一个是根目录的node_modules目录。

## process全局变量

    process.argv获取命令行参数，argv[0]是nodejs执行程序的绝对地址，argv［1]是主模块的绝对路径，所以命令行参数
    从argv[2]这个位置开始.
## __dirname

    __dirname的值是该文件所在目录的路劲，同一个程序中的不同文件内的__dirname的值都不同。

## Node的HTTP接口
有个会调函数接收两个参数req,res(习惯这么写而已可以换成别的)，每接到一条HTTP请求,先解析HTTP报头,然后作为req的一部分和res一起触发回调函数,但是HTTP报文的请求体不会在回调被触发前解析。
之后需要调用res.end()方法结束响应，在这句代码之前可以是任何逻辑。
修改响应头要在res.end 或者res.write之前


