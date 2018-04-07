# 模型

模型是关于数据的单一、明确的信息源，它包含存储数据的必要字段及操作数据的方法。一般来说，一个模型对应一个数据库表。 
基础知识： 
> * 每个模型都是继承自[django.db.models.Model](https://docs.djangoproject.com/zh-hans/2.0/ref/models/instances/#django.db.models.Model)的一个Python子类
> * 模型的每个属性都代表一个数据库字段 
> * 基于以上这些，Django提供了一个自动生成的数据库访问API。详见[数据查询](queries.md)

## <span id="simple-example">简单示例</span>

以下示例定义了一个包含first_name和last_name属性都Person模型 

```
from django.db import models

class Person(models.Model):
	first_name = models.CharField(max_length=30)
	last_name = models.CharField(max_length=30)

```

first_name和last_name是模型类的两个属性，每个属性代表一个数据表的字段 
上面的Person模型将创建一个下面这样的数据库表： 

```
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

一些技术要点： 
> * 表名 **myapp_person** （译者注：myapp指代你的Django app名称）是自动从模型元数据派生出来的，可以修改。查阅[表名设置](https://docs.djangoproject.com/zh-hans/2.0/ref/models/options/#table-names)获取详细内容。 
> * id字段将自动添加，也可以自定义。详见[自动添加主键字段]() 
> * 这个例子中的建表SQL用的是PostgreSQL语法格式，Django会根据[配置文件]()中指定的数据库来定制SQL语句。 

---

## 使用模型

一旦你定义了模型，你需要告诉Django你想要使用这些模型。可以编辑配置文件，在 **[INSTALLED_APPS](https://docs.djangoproject.com/zh-hans/2.0/ref/settings/#std:setting-INSTALLED_APPS)** 中添加包含应用名称。
举个例子，如果模型定义在模块 **myapp.models** （由 **manage.py startapp** 脚本为应用创建的包结构）。 **[INSTALLED_APPS](https://docs.djangoproject.com/zh-hans/2.0/ref/settings/#std:setting-INSTALLED_APPS)** 应该看起来像这样：

```
INSTALLED_APPS = [
    #...
    'myapp',
    #...
]
```

一旦你增加了新的应用到 **[INSTALLED_APPS](https://docs.djangoproject.com/zh-hans/2.0/ref/settings/#std:setting-INSTALLED_APPS)** 中，一定要执行 **[manage.py migrate](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-migrate)** 命令，在此之前，一般会执行迁移命令 **[manage.py makemigrations](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-makemigrations)** 来先生成迁移文件。 

---

## 字段

模型最重要的部分，也是唯一必需的部分——是数据表定义的字段列表。这些字段被定义为模型类的属性。请确保字段名不与[模型API](https://docs.djangoproject.com/zh-hans/2.0/ref/models/instances/)的关键字发生冲突，如clean, save, delete等。


例子：

```
from django.db import models

class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)

class Album(models.Model):
    artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```

## 字段类型

每个字段都应该是一个适当字段类的实例。Django使用字段类来确定一些事情：
> * 告知数据库应该以那种类型来存储数据（例如INTEGER, VARCHAR, TEXT等）
> * 渲染表单时，字段对应哪种HTML标签（例如<input type="text">, <select>等）
> * Django管理页面和自动生成的表单中需要的最小验证需求

Django带有几十种内置字段类型，你可以在[模型字段指南](https://docs.djangoproject.com/en/2.0/ref/models/fields/#model-field-types)中看到完整列表。你也可以很方便的编写自定义的字段类，详见[编写自定义模型字段类](https://docs.djangoproject.com/en/2.0/howto/custom-model-fields/)

---

## 字段选项

每一个字段类都有一组特定的参数（相关文档参见[模型字段指南](https://docs.djangoproject.com/en/2.0/ref/models/fields/#model-field-types)）。举个例子，[CharField](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.CharField)（以及它的子类）通过[max_length](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.CharField.max_length)参数来指定数据库中 **VARCHAR** 字段的长度。

除了特定参数，还有一组通用的字段参数，完整解释参见[通用字段参数指南](https://docs.djangoproject.com/en/2.0/ref/models/fields/#common-model-field-options)，这里有一个常用参数摘要：






