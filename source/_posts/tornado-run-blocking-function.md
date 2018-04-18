---
title: Tornado执行阻塞函数
date: 2018-04-18 09:32:17
categories: Python
tags: Tornado
---
## 使用Tornado时执行阻塞函数的姿势
很多人使用Tornado的姿势其实都是不对的，很多人都是知乎style地使用Tornado，当然这话是大佬说的。
我脑补了下才反应过来什么叫知乎style，其实就指用Tornado，但是代码里却很多阻塞的。Tornado的文档
guide里异步和非阻塞I/O节第二段就说了：
`为了尽量减少并发连接造成的开销，Tornado使用了一种单线程事件循环的方式。
这就意味着所有的应用代码都应该是异步非阻塞的, 因为在同一时间只有一个操作是有效的.`
敲黑板了，这是官方原话，所以这就是正确的使用Tornado的姿势没跑了。
## 所以有以下的操作让耗时的阻塞操作变异步的方式
董伟明大佬的博文[使用tornado让你的请求异步非阻塞](http://www.dongwm.com/archives/shi-yong-tornadorang-ni-de-qing-qiu-yi-bu-fei-zu-sai/)
说了我知道的和不知道的方式，欢迎补充。
## 官方的做法是？
我想改变公司的知乎style代码，就去啃了下文档，才有了这篇文章的由来，在翻最新的5.x版本文档里，我想起了以前有些卡时间的操作都
用了`run_on_exexutor`于是就翻到这个函数的文档，然后发现文档里多了句这个` In general, using run_in_executor 
when calling a blocking method is recommended instead of using this decorator when defining a method.`
什么时候出现的难道是我以前看英文没认真。于是搜了下这个函数在[这里](http://www.tornadoweb.org/en/stable/guide/coroutines.html#calling-blocking-functions)
在文档里看到了这个示例:

```
@gen.coroutine
def call_blocking():
    yield IOLoop.current().run_in_executor(blocking_func, args)
```
然后跟一个大佬分享了，大佬说那这个blocking_func是任意的都可以么，于是带着这个疑问我再翻了下文档，
点过去看到了这个函数的声明与注释，原来是5.0新加的。`Runs a function in a concurrent.futures.Executor` 看
到concurrent你们会想到啥，反正我脑子里冒出了ThreadPool和ProcessPool。
```
  def run_in_executor(self, executor, func, *args):
        """Runs a function in a ``concurrent.futures.Executor``. If
        ``executor`` is ``None``, the IO loop's default executor will be used.

        Use `functools.partial` to pass keyword arguments to ``func``.

        .. versionadded:: 5.0
        """
        if ThreadPoolExecutor is None:
            raise RuntimeError(
                "concurrent.futures is required to use IOLoop.run_in_executor")

        if executor is None:
            if not hasattr(self, '_executor'):
                from tornado.process import cpu_count
                self._executor = ThreadPoolExecutor(max_workers=(cpu_count() * 5))
            executor = self._executor
        c_future = executor.submit(func, *args)
        # Concurrent Futures are not usable with await. Wrap this in a
        # Tornado Future instead, using self.add_future for thread-safety.
        t_future = Future()
        self.add_future(c_future, lambda f: chain_future(f, t_future))
        return t_future
```
源码如上, 清晰明了，官方也是简单粗暴，耗时的我再开个线程池来处理就好了。
以后如果阻塞的函数都可以用这个run_in_executor了。这样你的代码就能用正确的姿势
跑在Tornado框架里了。


