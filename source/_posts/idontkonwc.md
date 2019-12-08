---
title: I don't know c
date: 2019-11-29 19:24:16
categories: C
tags:
    - syntax
---
## 关于scanf
我之前大学学了和用了好几年的c，从来没注意到scanf函数的第一个参数是在定义模式匹配

## 多维数组做参数
对于多维数组，是按行放的。例如:
```c
   int ex[10][10];
```
是先存第0行，然后第一行，然后第二行，在给形式参数赋值的时候，是把指向数组第一个元素的指针给了函数。
假如:
```c
   int test(int ex[][10], int n)
   {
       int sum = 0;
       for (int i = 0; i < n; i++)
        for (int j = 0; j < 10; j++)
            sum += ex[i][j];
       return sum;
   }
```
<!-- more -->
那么他取值的时候:
1 用i乘a每行的大小
2 把乘积的结果加到ex表示的地址上
3 用j乘以ex中每个元素的大小
4 把乘积的结果加到2 算出的地址上
为了产生这个指令，编译器必须知道ex的每一行的大小。行的大小由列决定。
所以多维数组不能省略列数。

## 变长数组

额，可能之前用的太少，我居然完全没有这个记忆。
```c
   int n;
   scanf("%d", &n);
   int v[n];
```

变长数组作为函数参数的时候需要这样子使用
```c
  int sum(int n, int v[n])
```


## static local variable

整个程序运行期间不释放，具有区块作用域对其他函数隐藏，允许同名函数再使用。
简单的代码验证这点。
```c
#include <stdio.h>

void f(int n)
{
    static int i=0;
    printf("n:%d, i:%d\n", n, i);
    i = n;
}

int main(void)
{
    f(1);
    f(2);
    return 0;
}
```

## 类型
之前看编程范式相关看过类型系统是描述内存布局的概念
在C里面非常直观的体现了这个概念

## `const int *p` 与 `int * const p`
前者表示p指向的对象不可以更改:

```C
int a = 10;
*p = 10; // wrong
p = &a;  // legal
```

后者表示p本身不可以更改:
```C
int a = 10;
*p = 0; //  legal
p = &a; // wrong
```

## 指针数组和数组指针
指针数组: 元素是指针的数组
数组指针: 指向数组的指针

`int *` 是指向整数的指针
`int (*)[10]` 是指向整数数组的指针
`int *p[10]` 是每个元素是指向整数的指针的数组叫指针数组

