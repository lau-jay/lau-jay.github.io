---
layout: post
title: "select sort"
date: 2016-08-27 21:30:07 +0800
comments: true
categories: python
tags: algorithms sort
---

## select sort
闲着没事，就想起写写排序的算法，因为从来没用Python写过算法，依据算法的原理很快
就撸了个选择排序：

    def select_sort(ary):
        for i in range(len(ary)):
            tmp = ary[i]
            index = ary.index(min(ary[i:]))
            ary[i] = ary[index]
            ary[index] = tmp
        return ary

咋一看没有问题，然后测试的结果让我吃了一惊，很小的测试集合。

    test = [23, 455, 234, 23423, 23, 4, 5, 6, 7, 3]

结果是：
    result = [3, 4, 5, 6, 7, 234, 23423, 455, 23, 23]

对数据研究后的一个猜测是:因为数据存在重复。
于是将重复值去除结果就对了。于是开始debug，结果让我哭笑不得。
index的一个特性是返回第一个符合传入参数的元素的index。
而我在这句:

    index = ary.index(min(ary[i:]))

中ary.index就成了全list索引而不是扣除已经排好序的剩余list元素中比较。

改成index = i + ary[i:].index(min(ary[i:]))

    def select_sort(ary):
        for i in range(len(ary)):
            tmp = ary[i]
            index = i + ary[i:].index(min(ary[i:]))
            ary[i] = ary[index]
            ary[index] = tmp
        return ary


就OK了，这其中还有个坑是如果漏了i,那么左值index仅仅是子序列ary[i:]的index，
而不是整个ary的index，这样必然会给等下的swap造成问题的。最后利用python
交换值的语法糖改写代码:

    def select_sort(ary):
        for i in range(len(ary)):
            index = i + ary[i:].index(min(ary[i:]))
            ary[i], ary[index] = ary[index], ary[i]
        return ary

这样只需要几行的选择排序就完成了，不过我没测试过这样反复的切片和使用min开销会不会很大，
这短短几行给我的一个教训是，使用对象的方法一定要注意全面了解方法。



