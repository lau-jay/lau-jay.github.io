---
title: base64上传图片
date: 2017-11-25 20:23:13
categories: python
tags: python
---
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


