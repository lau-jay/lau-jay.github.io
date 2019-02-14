---
title: django-model
date: 2017-06-01 22:17:09
categories: python
tags: 
    - django
    - model
---
## model
### 模型与表
Django的一个模型对应一个数据库表，模型的每个属性对应表的一列,  属性名就是列名。
每个属性的类型就是对应数据库列的类型:

   CharField(max_length=30) : varchar(30)
   ManyToManyField(Author) :  join table  Author

Django在没有明确定义的时候会为每个模型定义一个自增量整数主键字段id //id = models.AutoField(primary_key=True)


使用 Django 模型 API 创建的对象不会自动保 存，只能自己动手调用 save() 方法:

    p1 = Publisher(...)
    # 此时，p1 尚未保存到数据库中!
    p1.save()
    # 现在保存了。
### 查找

    like: field__contains, 例如 model.objects.filter(name__contains='xxx'),
    filter(mobile__contains='18') 返回一个查询集合 这里的field__contains中间一定是双下划线
    还有field__icontains=x查询field值里包含x值的数据，忽略大小写
    limit 1: model.objects.filter(city='xiamen')[0] 切片方式返回一个对象,
    不支持负索引, 不过只要反向排序再索引就好了

### 排序

    Order By: model.objects.order_by("address") 正序排序 model.objects.order_by("-address") 反序排序

### 更新

    model.objects.filter(id=xx).update(name="pyclear") 这样只更新一个数字这样比较高效
    p = model.objects.get(id=5) 
    p.name = 'other'
    p.save() 这样会把所有列更新一次所以会比较慢

### 删除

    p.delete() 直接再该对象上调用方法

### 同步模型的改动到迁移中

    python manage.py check
    python manage.py makemigrations books

