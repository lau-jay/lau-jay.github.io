---
layout: post
title: "elixir set"
date: 2018-11-07 22:26:42 +0800
comments: true
categories: elixir
tags: elixir
---
# list
值的简单集合，可以包含不同的数据类型
```elixir
iex> [3.14, :pie, "Apple"]
[3.14, :pie, "Apple"]
```
list内部实现为链表, 拼接使用++

```elixir
iex(44)> h ++/2

                               def left ++ right

  @spec list() ++ term() :: maybe_improper_list()

Concatenates a proper list and a term, returning a list.

The complexity of a ++ b is proportional to length(a), so avoid repeatedly
appending to lists of arbitrary length, e.g. list ++ [item]. Instead, consider
prepending via [item | rest] and then reversing.

If the right operand is not a proper list, it returns an improper list. If the
left operand is not a proper list, it raises ArgumentError.

Inlined by the compiler
```

在elixir（erlang）中，函数和操作符号的名字由名字和元数字组成，元数代表名字接受的参数的个数。

列表减法对于右边列表出现的每个值，都会从左边边列表移除第一个与之相等的值。

head和tail是列表常见的， head是列表第一个元素，tail是除了head的所有

```elixir
iex> hd [3.14, :pie, "Apple"]
3.14
iex> tl [3.14, :pie, "Apple"]
[:pie, "Apple"]

```

# tuple

元组实现为顺序表，所以获取长度很快，修改很慢，新元组必须重新在内存拷贝一份。

```elixir
iex> File.read("path/to/existing/file")
{:ok, "... contents ..."}
iex> File.read("path/to/unknown/file")
{:error, :enoent}

```

这里返回值为元组

# Keyword list



```elixir
iex> [foo: "bar", hello: "world"]
[foo: "bar", hello: "world"]
iex> [{:foo, "bar"}, {:hello, "world"}]
[foo: "bar", hello: "world"]
```

可以理解为是key一定为atom类型的字典

# Map

```elixir
iex> map = %{:foo => "bar", "hello" => :world}
%{:foo => "bar", "hello" => :world}
iex> map[:foo]
"bar"
iex> map["hello"]
:world
iex> key = "hello" 
"hello"
iex> %{key => "world"} #变量也可以做key
%{"hello" => "world"}
```

跟Python的字典类似的结构，如果key是atom的可以不需要`=>`符号