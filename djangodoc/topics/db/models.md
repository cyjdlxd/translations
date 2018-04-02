# 模型

> 模型是关于数据的单一、明确的信息源，它包含存储数据的必要字段及操作数据的方法。一般来说，一个模型对应一个数据库表。 
> 基础知识： 
> * 每个模型都是继承自[django.db.models.Model](https://docs.djangoproject.com/zh-hans/2.0/ref/models/instances/#django.db.models.Model)的一个Python子类
> * 模型的每个属性都代表一个数据库字段 
> * 基于以上这些，Django提供了一个自动生成的数据库访问API。详见[数据查询](queries.md)

## 简单示例

> 以下示例定义了一个包含first_name和last_name属性都Person模型 

```
from django.db import models

class Person(models.Model):
	first_name = models.CharField(max_length=30)
	last_name = models.CharField(max_length=30)

```

> first_name和last_name是模型类的两个属性，每个属性代表一个数据表的字段 
> 上面的Person模型将创建一个下面这样的数据库表： 

```
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

> 一些技术要点： 
> * 表名myapp_person（译者注：myapp指代你的Django app名称）是自动从模型元数据派生出来的，可以修改。查阅[表名设置](https://docs.djangoproject.com/zh-hans/2.0/ref/models/options/#table-names)获取详细内容。
> *
> *
> *
> *
