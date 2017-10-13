---
layout: post
title: "elixir one"
date: 2016-11-11 23:26:42 +0800
comments: true
categories: elixir
tags: elixir
---
## 除法

    div 10 ， 2 => 5
    10 / 2 => 5.0

## 求余

    rem 10， 3 => 1

## 匿名函数

    sum = fn x, y -> x + y end
    sum 10, 9 => 19

    匿名函数是闭包，当前作用域(scope)内的其它变量都可以访问
    x = 9
    (fn x -> x + 1 end).(x) => 10
    x => 9


## `=` 不是赋值， 而是模式匹配(pattern match)  

## 一些基础数据类型
* boolean
    * true or false
    * method: is_boolean
    * h is_boolean (类似python的help)
    * elixir 文档描述函数一般是:
        `<函数名>/<元数>
        元数可以理解为函数的参数个数`
* number
    * integer
    * float (64位双精度浮点)
* atom
    * 类似ruby的符号(symbol）
    * 字符前加:就行了，大写字母开头的变量是一种原子，其等价于`:Elixir.变量名`
    * `true == :true` # true
    * is_atom(false) # true
    * is_atom(:flase) # true
*  string
    * 单引号定义字符串列表，双引号定义字符串
    *  string interpolation  `"hello #{:world}"`
    * byte_size("hellö") 返回所暂byte数
    * String.length("hellö") 返回字符数
    * "foo" <> "bar" # "foobar"
* list
    * `++` or `--` 拼接列表或消除列表
    * method: length，hd，tl
    * 当list里的整数是ascii打印数值的时候会返回一个单引号的字符串列表
       ```
         iex> [11, 12, 13]
         '\v\f\r”
       ```
- tuple
    * method: elem

## operators
    * or, and , not # 只接受boolean
    * ||, &&, ! # accept arguments of any type
    * number < atom < reference < function < port < pid < tuple < map < list < bitstring

## elixir-vim plug
add a line to .vimrc:
    `Plug 'elixir-lang/vim-elixir'`
last run in vim:
  `:PlugInstall`
