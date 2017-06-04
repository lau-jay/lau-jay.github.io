---
title: argparse
date: 2016-12-09 22:19:43
categories: python
tags: stdlib
---
argparse是一个命令行解析的标准库，参数可以根据add_argument()的action选项触发不同的action。
```
#arg-demo.py
import argparse

def get_args():
    parser = argparse.ArgumentParser(
        description="demo argument parser",
        epilog="example usage"
    )
    parser.add_argument('-l',action="show", required=True,
        help='Help text for option x')
    parser.add_arguemtn('-a',help="show all file",default=False)
    print(parser.parse_args()) # parse_args()用来参数解析

if __name__ == '__main__':
    get_args() 
$python3 arg_demo.py -x testing # Namespace(l='testing', a=False)
```
required=True保证了在命令调用的时候一定要上-l参数,也就是其他都是可选参数而－l参数是必须的。输出的结果显示带参数执行会在namespace里加上参数和参数值, 完全可以理解为命令空间内的全局变量。
default保证了－a参数的默认值位False,实际上如果不加的话，如果使用的时候没使用这个参数。那么默认值为None。

显然，现在例子中的都是可以同时使用的参数。有些时候一些参数提供的功能没法同时使用，这时候需要使用互斥分组,`add_mutually_execlusive_group`
```
group = parser.add_mutually_exclusive_group()

grour.add_argument('-l','--execute',action="show",
        help='Help text for option x')
group.add_arguemtn('-a',help="show all file",default=False)
```
如果同时使用这两参数的话，会报错:
error: argument -a: not allowed with argument -l/--execute


