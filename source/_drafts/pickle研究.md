---
title: pickle研究
tags:
---
pickle模块用于Python对像的序列化为适合存储的字节流，并可以反序列化字节流为Python对象
```
   import pickle
   with open('myfile','wb') as f:
       pick.dump(x,f)
       pickle.dump(y,f)
   load(file)
```
