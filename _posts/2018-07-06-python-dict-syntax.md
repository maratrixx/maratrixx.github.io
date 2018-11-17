---
layout:     post
title:      "Python学习笔记07-字典"
subtitle:   ""
date:       2018-07-06 15:10:39
author:     "maratrix"
header-img: "img/home-bg.jpg"
tags:
    - python
---

## 什么是字典

Python 内置了字典 `dict` 的支持，可以存储任意类型对象，在其他语言中也称为 `map`，使用键 - 值（key-value）对存储，具有极快的查找速度。

字典的每个键值对使用冒号`:`分割，每个对之间使用逗号`,`分割，整个字典包括在花括号`{}`里面：

```
dd = {'name':'maratrix', 'age': 18}
```

## 字典的特性

字典值可以是任何数据类型，但键必须是不可变的，比如：字符串、数字、元组。

### 关于键有两点要求：

- 不允许同一个键出现两次，如果同一个键被赋值两次，后一个值会覆盖掉前一个值。

- 键必须不可变，所以可以用数字，字符串或元组，而用列表就不行。

```
>>> d = {'a':'字符串', 1:'数字', 2.3:'数字', (1,2):'元组'}
```

## 访问字典里的值

把相应的键放入方括号`[]`内：

```
>>> dd = {'name': 'maratrix', 'age': 18}
>>> dd['name']
'maratrix'
```

## 判断键是否存在

如果 key 不存在，字典就会报错：

```
>>> dd['sex']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'sex'
```

要避免 key 不存在的错误，有两种办法：

### 一是通过 `in` 判断 key 是否存在：

```
>>> 'sex' in dd
False
>>> 'name' in dd
True
```

### 二是使用字典提供的 `get` 方法：

如果 key 不存在，会返回 `None` 或者自己指定的默认返回值。

```
>>> dd.get('sex', -1)
-1
>>> dd.get('sex') == None
True
```

**注意：**返回None的时候 Python 的交互环境不显示结果。

## 修改字典

```
>>> dd = {'name': 'maratrix', 'age': 18}
>>> dd['name'] = 'Hanson'
```

## 删除字典

清空字典使用 `clear`：

```
>>> dd = {'name': 'maratrix', 'age': 18}
>>> dd.clear()
>>> dd
{}
```

删除字典使用 `del`:

```
>>> dd = {'name': 'maratrix', 'age': 18}
>>> del dd
```

删除字典中的一个值使用 `del` 或者 `pop`：

```
>>> dd = {'name': 'maratrix', 'age': 18}
>>> del dd['name']
>>> dd.pop('age')
18
>>> dd.pop('sex', '不存在的键')
'不存在的键'
```

Python 字典 `pop()` 方法删除字典给定键 `key` 所对应的值，返回值为被删除的值。`key` 值必须给出。 否则，返回 `default` 值。