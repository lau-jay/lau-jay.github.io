---
title: Python3 bytes/str
date: 2017-10-17 13:52:34
categories: python
tags: python
---

### error

```
cPickle.PicklingError: Can't pickle

```

### shell

```
python3 -m trace -tg --ignore-dir /Library $1
```

### bug code

```
def process_way():
    workers = 10
    with futures.ProcessPoolExecutor(workers) as executor:
        futs = {executor.submit(blocking_way) for i in range(10)}
    return len([fut.result() for fut in futs])
```

### reason
```
Pool methods all use a queue.Queue to pass tasks to the worker processes.
Everything that goes through the queue.Queue must be pickable.
So, multiprocessing can only transfer Python objects to worker processes which can be pickled.
Functions are only picklable if they are defined at the top-level of a module, bound methods are not picklable.
```

### fixed

```
def process_way():
    workers = 10
    with futures.ThreadPoolExecutor(workers) as executor:
        futs = {executor.submit(blocking_way) for i in range(10)}
    return len([fut.result() for fut in futs])
```
