---
title: aiomysql
date: 2018-01-06 15:30:17
tags: python
---
突然兴起，去试了试aiomysql, 记录下遇到的问题。

```
import asyncio
import aiomysql

from config import db_config

loop = asyncio.get_event_loop()

async def test_example():
    conn = await aiomysql.connect(
        host=db_config['host'],
        port=db_config['port'], # 第一个坑，之前用pymysql没单独指定port的, 是跟host一起写的
        db=db_config['name'],
        user=db_config['user'],
        charset='utf8', # 第二个坑，没默认编码，导致取不出中文
        password=db_config['password'],
        loop=loop
    )
    cur = await conn.cursor(aiomysql.DictCursor) # 第三个坑，不指定dict的话捞出来的是tuple类型的数据
    await cur.execute("SELECT * FROM question ORDER BY create_time DESC LIMIT 20")
    r = await cur.fetchall()
    print(r)
    await cur.close()
    conn.close()

loop.run_until_complete(test_example())
```

整体体验还不错，这是非pool的写法，pool 的写法如下
```
import asyncio
import aiomysql


async def test_example(loop):
    pool = await aiomysql.create_pool(host=db_config['host'], port=db_config['port'],
                                      user=db_config['user'], password=db_config['password'],
                                      db=db_config['name'], loop=loop)
    async with pool.acquire() as conn:
        async with conn.cursor(aiomysql.DictCursor) as cur:
            await cur.execute("SELECT * FROM question ORDER By create_time DESC LIMIT 20")
            print(cur.description)
            r = await cur.fetchall()
            print(r)
    pool.close()
    await pool.wait_closed()


loop = asyncio.get_event_loop()
loop.run_until_complete(test_example(loop))
```

目前没听说有公司生产环境大规模用，但是感觉应该是有用，只是没说而已吧。
或者都用celery去了?

