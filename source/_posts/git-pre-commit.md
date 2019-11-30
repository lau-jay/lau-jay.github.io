---
title: git pre-commit workflow
date: 2019-03-06 20:14:29
categories: git
tags: git
---
> 之前看了程序人生的一篇code is law，做了点小小的实践。想起在QQ群看到一个人的分享截图，于是就想将之前做的实践查到的资料删减翻译下整理成文章，如果万一有团队内部的技术分享会还是可以分享下的。

我想遵循pep8，我不想手动格式化代码，我不想每次手动执行flake8来检查，甚至我不想手动执行mypy去检查我代码。

那么有什么办法呢，作为开发，日常最常执行的操作git commit必然是其中之一，无论是通过IDE的GUI工具还是命令行的方式。

通过git提供的pre-commit的hook可以在commit的时候将代码格式化，规范检查，测试等执行完毕，而不需要工程师手动做相关的操作。流程类似这样的：

![workflow](https://ljvmiranda921.github.io/assets/png/tuts/precommit_pipeline.png)

通过采用*pre-commit* 这个Python编写的库只需要以下几步就可以完成上述任务。

*  `pipenv install -dev pre-commit`
* 编辑.pre-commit-config.yaml
* 执行pre-commit install 以便在.git/目录中安装git hooks

<!-- more -->
比如你想用black格式化代码，flak8检查代码规范，单元测试那么例子如下：

```yaml
repos:
    repo: local
    hooks:
    - id: black
      name: black
      language: system
      entry: black
      types: [python]
    - id: flake8
      name: flake8
      language: system
      entry: flake8
      types: [python]

    - id: unitest
      name: unitest
      language: system
      entry: ./test.sh
      types: [python]
```

repos是顶层的，每个hook都可以增加一个repo的section。
repo存储库映射告诉pre-commit从哪里获取提现实现好的hook比如这里的flake8可以改为远程的https://github.com/pre-commit/pre-commit-hooks
在上面这个例子中，由于我都在本地装了entry对应的文件，所以直接使用local
entry 表示可执行的文件或者命令
types 表示对哪些文件的更改起作用
language 表示hook是哪些语言实现的，这里直接偷懒用系统,让他自己找

有时候我们自己的标注和pep8的略有不同，那么可以在当前目录下增加 `.flake8` 来告诉flake8我们自己的客制化。

```toml
[flake8]
ignore = E203, E266, E501, W503, F403, F401
max-line-length = 79
max-complexity = 18
select = B,C,E,F,W,T4,B9
```



* 本文章原文及本文的图片来自[这里](https://ljvmiranda921.github.io/notebook/2018/06/21/precommits-using-black-and-flake8/) 版权归原作者所有


