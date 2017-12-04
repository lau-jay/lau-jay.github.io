---
title: base64上传图片
date: 2017-11-25 20:23:13
categories: python
tags: python
---
## 缘由
在做一个图片上传的微服务, 使用的是tornado, 利用tornado自带的表单上传,
使用self.request.files, 结果移动端表示他们不会构造表单格式上传文件。
由于之前没写过base64传图就特地去了解了下, 写了个接口出来。感觉不常写会忘,
还是记录下吧。
## Convert Image To String
```
import base64
with open('old.png') as img:
    b64_str = base64.b64encode(img.read())
```

## Convert String To Image
```
with open('new.png') as img:
    img.write(image_str.decode('base64'))
```

## [Get Image File Size From Base64 String](https://stackoverflow.com/questions/11761889/get-image-file-size-from-base64-string)
Multiply the length of the data by 3/4, since encoding turns 6 bytes into 8.
If the result is within a few bytes of 4MB then you'll need to count the number of = at the end.
```
def size(b64_str)
    return (len(b64_str) * 3) / 4 - b64_str.count('=', -2)
```


