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

### [null](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.null)

如果值是True，Django将把空值转为null存入数据库。默认是Fasle。

### [blank](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.blank)

如果值是True，当前字段的值将允许为空。默认是False。

请注意blank与null的区别，null是纯粹数据库相关的，而blank是验证相关的。如果一个字段设置blank=True，表单验证将允许该字段输入空值。如果一个字段设置blank=False, 这个字段就是必填的。

### [choices](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.choices)

一个可迭代的二维元组（list或者tuple等）可以作为字段的choices参数，如果给出了这个参数，对应的默认表单组件（管理页面表单）将会是selec选择框，选项限定为给定的choices元组中的子元组元素。

一个选择列表例子：

```
YEAR_IN_SCHOOL_CHOICES = (
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
)
```

每个子元组中的第一个元素将被存入数据库中，第二个元素用于字段在表单里对应的组件中展示。

对于模型实例中设定 **choices** 参数的字段，可以通过 **[get_FOO_display](https://docs.djangoproject.com/en/2.0/ref/models/instances/#django.db.models.Model.get_FOO_display)** 方法获取所有的展示值，例如：

```
from django.db import models

class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
    name = models.CharField(max_length=60)
    shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

```
>>> p = Person(name="Fred Flintstone", shirt_size="L")
>>> p.save()
>>> p.shirt_size
'L'
>>> p.get_shirt_size_display()
'Large'
```

### [default](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.default)

字段的默认值，可以是一个值或者回调对象。如果可回调，每次创建新对象时都会调用它。

### [help_text](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.help_text)

用于展示在表单组件的额外帮助信息，即使该字段未用于表单，作为文档帮助信息也是很有用的。

### [primary_key](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.primary_key)

如果值是True，该字段将作为模型的主键。

如果你没有指定任何字段为主键，Django将自动添加一个[IntegerField](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.IntegerField)字段作为主键（译者按：自动添加的主键是非自增的。），因此，除非你想特别指定一个主键，否则完全可以不用手动指定主键。更多信息，参阅[自动主键字段](https://docs.djangoproject.com/en/2.0/topics/db/models/#automatic-primary-key-fields)。

主键字段是只读的，如果你想修改一个已存在的模型实例的主键值并执行保存，只会新插入一条数据并创建一个新的模型实例，而不是修改你想修改的那个模型实例的主键。例如：

```
from django.db import models

class Fruit(models.Model):
    name = models.CharField(max_length=100, primary_key=True)
```

```
>>> fruit = Fruit.objects.create(name='Apple')
>>> fruit.name = 'Pear'
>>> fruit.save()
>>> Fruit.objects.values_list('name', flat=True)
<QuerySet ['Apple', 'Pear']>
```

### [unique](https://docs.djangoproject.com/en/2.0/ref/models/fields/#django.db.models.Field.unique)

如果值为True，该字段将被设置唯一索引。

以上只是常用字段参数的一个简短摘要，全面详细的内容参阅[常用模型字段参数参考指南](https://docs.djangoproject.com/en/2.0/ref/models/fields/#common-model-field-options)
















