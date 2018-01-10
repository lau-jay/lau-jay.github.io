---
title: aioredis with tornado
date: 2018-01-10 15:30:17
categories: python
tags: aio
---
今天尝试了下, 把aioredis用上, 由于aioredis是基于asyncio的, 所以在tornado里需要把loop
换成asyncio, 整体觉得还是蛮别扭的。有个aredis是国内bilibili的工程师写的, 感觉应该使用
起来会更自然点。
主体代码如下:

```
import tornado.web
import asyncio
from tornado.platform.asyncio import AsyncIOMainLoop

class Application(tornado.web.Application):
    def init_with_loop(self, loop):
        self.redis = loop.run_until_complete(
            aioredis.create_redis(
                 ('localhost', 6379),
                 loop=loop)
        )
app = Application(param) # your param
loop = asyncio.get_event_loop()
app.init_with_loop(loop)
loop.run_forever()
```
[参考](https://github.com/rudyryk/python-samples/blob/master/hello_tornado/hello_asyncio.py)

