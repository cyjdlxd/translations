# 为模型提供初始数据

> 有时候，在第一次设置应用程序的时候，用硬编码的初始化数据来填充数据库是非常便利的。在Django中，可以通过数据迁移或夹具（译者按：fixtures，夹具，可理解为一种静态资源或静态数据生成器, *百度百科称，用来迅速、方便、安全地安装工件的装置，都可称为[夹具](https://baike.baidu.com/item/%E5%A4%B9%E5%85%B7/5481848?fr=aladdin)*）来进行数据初始化。

## 通过迁移进行初始化

> 如果想自动加载应用程序的初始化数据，可以创建一个[数据迁移](https://docs.djangoproject.com/zh-hans/2.0/topics/migrations/#data-migrations)。数据迁移将在设置测试数据库时执行，将初始化数据填充到数据库。参阅[一些限制](https://docs.djangoproject.com/zh-hans/2.0/topics/testing/overview/#test-case-serialized-rollback)

---

## 通过夹具(fixtures)进行初始化

> 你也可以通过夹具(fixtures)提供初始化数据，但是，夹具的初始化数据不能自动加载，除非使用 **[TransactionTestCase.fixtures](https://docs.djangoproject.com/zh-hans/2.0/topics/testing/tools/#django.test.TransactionTestCase.fixtures)**  
> 夹具(fixture)是一种Django可识别的数据集合，Django了解如何将其倒入数据库。 
> 如果你已经准备了一些数据，最直截了当的创建夹具的方式就是执行命令 **[manage.py dumpdata](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-dumpdata)** 。或者，你也可以手写夹具，夹具可以用JSON、XML或者YAML(需要安装[PyYAML](https://www.pyyaml.org/))格式来撰写。查阅[序列化文档](https://docs.djangoproject.com/zh-hans/2.0/topics/serialization/)可以获得关于受支持的[序列化格式](https://docs.djangoproject.com/zh-hans/2.0/topics/serialization/#serialization-formats)的更多详细信息。 
> 下例展示了一个Person模型的JSON格式的夹具：

```
[
  {
    "model": "myapp.person",
    "pk": 1,
    "fields": {
      "first_name": "John",
      "last_name": "Lennon"
    }
  },
  {
    "model": "myapp.person",
    "pk": 2,
    "fields": {
      "first_name": "Paul",
      "last_name": "McCartney"
    }
  }
]
```

> 下例是YAML格式的夹具：

```
- model: myapp.person
  pk: 1
  fields:
    first_name: John
    last_name: Lennon
- model: myapp.person
  pk: 2
  fields:
    first_name: Paul
    last_name: McCartney
```

> 在你的应用目录创建 **fixtures** 目录，将夹具，存放在该目录。 

> 加载数据很简单：执行命令 **[manage.py loaddata](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-loaddata) <fixturename>** ，其中 **<fixturename>** 是你创建的夹具文件的名字。 
> 一旦你执行**[loaddata](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-loaddata)**命令，数据将从夹具重新加载到数据库。请注意，这意味着如果你修改了夹具中的数据，然后执行[loaddata](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-loaddata)命令，这些变化将同步到数据库，并覆盖数据库中原有的数据。

## Django如何寻找夹具文件

> 默认情况下，Django在每个应用目录下的**fixtures**目录查找夹具文件。你也可以通过修改配置文件中的**[FIXTURE_DIRS](https://docs.djangoproject.com/en/2.0/ref/settings/#std:setting-FIXTURE_DIRS)**配置一个附加的目录列表来告诉Django该去哪里查找夹具文件。 

> 在执行**[manage.py loaddata](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-loaddata)**命令时，你也可以指定一个夹具文件搜索目录，来替代之前配置的常用夹具文件目录。

---

>> ### 附注 

>> 夹具也可以用于[测试框架](https://docs.djangoproject.com/en/2.0/topics/testing/tools/#topics-testing-fixtures)，用以建立一致的测试环境。



[官方文档原文](https://docs.djangoproject.com/en/2.0/howto/initial-data/)


