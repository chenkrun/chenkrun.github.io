---
layout: post
title: jinja2学习笔记
date: 2018-09-26
categories: DevOps
tag: jinja2
excerpt: 本文介绍jinja2基础用法
---

## 一、前言

待完成

## 二、基础用法

#### 2.1 过滤器

**1.数值型过滤器**

```
- abs(number)
{% raw %}
{# 返回绝对值 20 #}
{{ -20 | abs }}
{% endraw %}

- filesizeformat(value, binary=False)
{% raw %}
{#
  将value转换成方便阅读的文件大小，如13MB, 12GB等。
  默认是采用十进制前缀，如Mega, Giga等。如果将binary
  设置为True，则采用二进制前缀，如Mebi, Gibi等。
#}
{{ 1234567890 | filesizeformat }}
{% endraw %}

- float(value, default=0.0)
{% raw %}
{# 将value转换成浮点型数，如果转换不成功，将返回default。 #}
{{ 1 | float }}
{% endraw %}
```

**2.字符串型过滤器**

```
- batch(value, linecount, fill_with=None)
{% raw %}
{# 
  将value中items分批成list，每批list个数linecount。
  如果指定fill_with，那么当最后一个list中的items个数
  不满足linecount时，用fill_with补足linecount。
#}
{% for batch in "123456789" | batch(5, "0") %}
    {{ batch }}
{% endfor %}
{% endraw %}

- capitalize(value)
{% raw %}
{# 将value的第一个字母改为大写，其余字母改为小写 #}
{{ "vALUE" | capitalize }}
{% endraw %}

- center(value, width=80)
{% raw %}
{# 先指定一块区域宽度为width，然后将value置于这个区域中心 #}
{{ "foo" | center(width=12) }}
{% endraw %}

- default(value, default_value=u'', boolean=False)
{% raw %}
{#
  当value未定义时，会返回default_value，否则返回value。
  当value等价于false且想返回default_value时（value为空），设置boolean为true。
#}
"Hello {{ name | default('No name') }}!"
{{ '' | d('the string was empty', true) }}
{% endraw %}
```

**3.对象型过滤器**

```
{% raw %}
{% set users=[{'name':'Tom','gender':'M','age':20},
              {'name':'John','gender':'M','age':18},
              {'name':'Mary','gender':'F','age':24},
              {'name':'Bob','gender':'M','age':31},
              {'name':'Lisa','gender':'F','age':19}]
%}
{% endraw %}

- attr(obj, name)
{% raw %}
{# 获取对象的属性 #}
{{ foo | attr("bar") }}
{% endraw %}

- dictsort(value, case_sensitive=False, by='key', reverse=False)
{% raw %}
{# 对字典排序，产生键值对。 #}
{% endraw %}

- first(seq)
{% raw %}
{# 返回列表的第一项 #}
{{ [1, 2, 3] | first }}
{% endraw %}

- format(value, *args, **kwargs)
{% raw %}
{# 对对象使用python字符串转换 #}
{{ "%s, %d" | format("Hello", 123) }}
{% endraw %}

- groupby(value, attribute)
{% raw %}
{#
  根据共同的属性对一系列对象进行分组。作为分组项的item将存储在grouper属性中，list中包含相同分组项的所有对象。
#}
{% for group in users | groupby('gender') %}
    {{ group.grouper }} {{ group.list }}
{% endfor %}

{% for grouper, list in users | groupby('gender') %}
    {{ grouper }} {{ list }}
{% endfor %}
{% endraw %}
```

**注意**：

- attr过滤器仅仅会寻找属性。当*foo*对象找不到*bar*属性时（getattr(foo, "bar")报AttributeError），会返回一个*未定义*对象。