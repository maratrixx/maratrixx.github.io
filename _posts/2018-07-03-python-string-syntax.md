---
layout:     post
title:      "Python学习笔记04-字符串连接总结"
subtitle:   ""
date:       2018-07-03 15:10:39
author:     "maratrix"
header-img: "img/home-bg.jpg"
tags:
    - python
---

在 Python 中字符串连接有多种方式，这里简单做个总结，应该是比较全面的了，方便以后查阅。

## 加号连接

第一种，通过`+`号的形式：

```
>>> a, b = 'hello', ' world'
>>> a + b
'hello world'
```

## 逗号连接

第二种，通过`,`逗号的形式：

```
>>> a, b = 'hello', ' world'
>>> print(a, b)
hello  world
```

但是，使用`,`逗号形式要注意一点，就是只能用于**print打印**，赋值操作会生成**元组**:

```
>>> a, b
('hello', ' world')
```

## 直接连接

第三种，直接连接中间有无空格均可:

```
print('hello'         ' world')
print('hello''world')
```

## `%`

第四种，使用`%`操作符。

在 Python 2.6 以前，`%` 操作符是唯一一种格式化字符串的方法，它也可以用于连接字符串。

```
print('%s %s' % ('hello', 'world'))
```

## `format`

第五种，使用`format`方法。

`format` 方法是 Python 2.6 中出现的一种代替 `%` 操作符的字符串格式化方法，同样可以用来连接字符串。

```
print('{}{}'.format('hello', ' world')
```

## `join`

第六种，使用`join`内置方法。

字符串有一个内置方法`join`，其参数是一个序列类型，例如数组或者元组等。

```
print('-'.join(['aa', 'bb', 'cc']))
```

## `f-string`

第七种，使用`f-string`方式。

Python 3.6 中引入了 Formatted String Literals（字面量格式化字符串），简称 `f-string`，`f-string` 是 `%` 操作符和 `format` 方法的进化版，使用 `f-string` 连接字符串的方法和使用 `%`操作符、`format` 方法类似。

```
>>> aa, bb = 'hello', 'world'
>>> f'{aa} {bb}'
'hello world'
```

## `*` 

第八种，使用`*`操作符。

```
>>> aa = 'hello '
>>> aa * 3
'hello hello hello '
```

## 小结

### 连接少量字符串时

推荐使用`+`号操作符。

如果对性能有较高要求，并且python版本在3.6以上，推荐使用`f-string`。例如，如下情况`f-string`可读性比`+`号要好很多：

```
a = f'姓名：{name} 年龄：{age} 性别：{gender}'
b = '姓名：' + name + '年龄：' + age + '性别：' + gender
```

###  连接大量字符串时

推荐使用 `join` 和 `f-string` 方式，选择时依然取决于你使用的 Python 版本以及对可读性的要求。 
