---
title: heapq
date: 2016-11-27 19:24:16
categories: python
tags: stdlib
---

# nlargest 和 nsmallest
找出集合中最大或最小的n个元素
如果使用参数key，可以让它们用在复杂的数据结构上

# 如果找n中最大或者最小的元素，n不大时

```
"""
   >>>nums = [1, 8, 2,  23, 7, -4, 18, 23, 42, 37, 2]
   >>>import heapq
   >>>heap = list(num)
   >>>heapq.heapify(heap)
   >>>heap
   [-4 , 2, 1, 23, 7, 2, 18, 23, 42, 37, 8]
   >>>heap[0] # min
   -4
"""
```
heapq.heappop(heap)每次都是当前剩下的最小的。
当然其实max和min才是最是适合的




