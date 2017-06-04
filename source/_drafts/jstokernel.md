---
title: jstokernel
date: 2017-02-16 22:28:22
categories: computer
tags: node
---
js to kernel 存在一种中间产物，叫做请求对象。
所有通过对象字面量创建的对象都连接到Object.prototype
for in会列出对象的所有属性，包括原型中的
delete可以删除对象的属性，并且不会触及原型链中的任何对象
全局污染，最好只创建唯一一个全局变量，es6大法好,let死区非常有必要。

对象字面量产生的对象连接到Object.prototype，
函数对象连接到Function.prototype(该对象本身连接到Object.prototype)

`var fibonacci = function(){
    var memo = [0,1];
    var fib = function(n){
        var result = memo[n];
        if(typeof result !== 'number'){
            result = fib(n-1) + fib(n-2);
            memo[n] = result;
        }
        return result;
    };
    return fib;
}();`






