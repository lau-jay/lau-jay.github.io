---
title: JS随笔
date: 2017-03-28 21:38:29
categories: js
tags: ecma
---
## 你不知道的JavaScript阅读笔记
## 作用域
JS是编译的，这是出乎很多人常识的一件事，编译通常在执行前。

```
引擎负责JS的编译以及执行过程
编译器负责语法分析以及代码生成(AST转为可执行代码)
作用域负责收集并维护标识符，并确定标识符的访问权限
```
作用域是根据名称查找变量的一套规则

变量的赋值，包括两个动作，编译器先在当前作用域声明一个变量(如果之前没声明过)然后运行时，引擎再在作用域中查找该变量
，找到就赋值，找不到就会抛异常。

引擎查找RHS（retrieve his source value）与LHS：变量出现在赋值操作左侧时进行LHS查询，出现在右侧时候进行RHS查询。
RHS就是单纯的找某个变量的值，LHS则是试图找到变量的容器本身，从而赋值。

### 作用域的嵌套
当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。
因此，在当前作用 域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量， 或抵达最外层的作用域(也就是全局作用域)为止。


## IIFE(立即执行函数)

    (function(){
        //dosomething
    })();

## bind
  es5提供了个内置的方法bind用来硬绑定一个对象，返回一个硬绑定了对象的的新函数。

    new 绑定:
    function foo(a){
        this.a = a;

    }
    var bar = new foo(2);
    console.log(bar.a);

## Object

JS会将非对象的字符串字面量自动转换为String对象，所以才能使用length或者split这些
属性或者方法。数值字面量也是一样。
null和undefined没有构造形式只有类似JSON的文字形式:
 
    var myObject = {
        add: function(x,y){},
     }

Date，没有文字形式，只有构造形式.
对象的属性名永远都是字符串,尽管数组下标中使用数字，但是属性名中数字会被
换成字符串。

## 访问属性
.要求属性名符合命名规范，而[]不做任何要求,所以[]内的字符串可以在代码中随意拼接。
ES6加入可计算属性名，在文字形式中使用[]包裹一个表达式来当属性名。

## 数组和函数
数组和函数都是对象的一个“子类型”,

## typeof

    typeof null 是一个对象，typeof返回的总是一个字符串
    typeof typeof 42;//string
    typeof 42先返回字符串"number",然后typeof "number"返回“string”

    typeof 不会对未声明过的变量报错
    //报错
    if(BEBUG){
        console.log('BEBUG is staring');
    }
    //不报错
    if(typeof DEBUG !== "undefined"){
        console.log('BEBUG is staring');
    }

ReferenceError: x is not defined 表示的是变量未定义

## 数组
数组是对象的一个“子类型”,可以容纳任何类型的值,无需预设大小.
delete 运算符可以从数据中删除单元，但是数组的length不会有变化。
由于数组也是对象，所以可以包含字符串键值，但是如果键值能被转成十进制数的话
会被当作数组的索引来处理的。

    var a = []
    a['13'] = 42;
    a.length; //14

## 值非值
undefined类型只有一个值，即undefined,null也只有一个值null
null指空值，undefined指没有值
NaN不是NaN也不是数字
Number.isNaN(..)可以用来判断NaN
Object.is(..) 来判断两个值是否绝对相等(能用==或者===的时候不要用这个方法)

## 值的引用
JS的引用只能是值的引用，并且引用相互间没有引用/指向的关系，一个值有十个引用，那么
这十个都是指向这个值的，一旦某个改变了值，其他指向的值不变

    var a = 2;
    b = a; // value copy
    b++;
    a; // 2
    b; // 3

简单值总是通过值复制的方式来赋值/传递，复合值总是通过对引用复制的方式赋值传递

    var c = [1,2,3];
    var d = c;
    d.push( 4 );
    c; // [1,2,3,4]
    d; // [1,2,3,4]


## this
this的绑定规则完全取决于调用位置(箭头函数内例外),但只有运行在
非'strict mode'下时，默认绑定才能绑定到全局对象, 严格模式下与调用位置无关。
隐式绑定规则: 当与变量同处于一个对象中的时候，this由于跟所处位置有关，会使用obj的
上下文来引用函数。
