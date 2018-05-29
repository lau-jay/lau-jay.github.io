---
title: pathlib
date: 2018-05-29 10:20:13
categories: python
tags: python3
---
在上次Python3 unknown 里我记录了Python3新加的一个库pathlib，还给了如下一个example
```
from pathlib import Path

directory = Path("/etc")
filepath = directory / "hosts"

if filepath.exists():
    print('hosts exist')

```
不得不说path的操作真是高频。。最近写的项目里我就用了好几次，写在这里增加下example:
```
from pathlib import Path
def load_tdk(section_name):
    root = Path(__file__).parent.parent
    config = root / Path("conf/tdk.json")
    tdk = json.loads(config.read_text())
    ...

```

这里将网站的seo的tdk.json文件导入并使用。省略了业务逻辑。这里展示的是
获取到配置文件的目录，并且可以直接用read_text()将文件打开并以文本方式读取，
还能以其他方式读取这里不展开了。

```
parent = Path(Path.cwd()).parent
private_key_pem = parent / Path("conf/app_private_key.pem")
public_key = parent / Path("conf/alipay_public.key")
app_private_key_path=private_key_pem.as_posix(),
alipay_public_key_path=public_key.as_posix(),
```
这是更熟悉之后的做法, Path.cwd() 类似于linux的命令pwd获取从root到当前路径的绝对地址
as_posix方法将包含文件名的路径转为字符串。


