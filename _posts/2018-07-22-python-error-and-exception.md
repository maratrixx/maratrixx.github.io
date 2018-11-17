---
layout:     post
title:      "Python 学习笔记20-错误和异常"
subtitle:   ""
date:       2018-07-22 10:08:30
author:     "maratrix"
header-img: "img/home-bg-art.jpg"
tags:
    - python
---

## 错误

- 语法错误：代码不符合解释器或编译器语法
- 逻辑错误：不完整或者不合法输入或者计算出问题

```
>>> if True print(123)
  File "<stdin>", line 1
    if True print(123)
                ^
SyntaxError: invalid syntax
```

函数 `print()` 被检查到有错误，是它前面缺少了一个冒号（:）, 并且会返回一个`SyntaxError: invalid syntax`的语法错误提示信息。

## 异常

- 程序遇到逻辑或者算法问题
- 运行过程中计算机错误（内存不够或者IO错误）

即便 Python 程序的语法是正确的，在运行它的时候，也有可能发生错误。运行期检测到的错误被称为异常。

大多数的异常都不会被程序处理，都以错误信息的形式展现在这里:

```
>>> 10 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```

错误信息的前面部分显示了异常发生的上下文，并以调用栈的形式显示具体信息。

## 异常捕获

Python 内置了 `try...except...else...finally...` 的错误处理机制。

```
try:
    print('...BEGIN...')
    print(10 / 0)
except ZeroDivisionError as e:
    print(e)
else:
    print('no error')
finally:
    print('...END...')
```

当我们认为某些代码可能会出错时，就可以用`try`来运行这段代码:
- 如果没有出错，并且提供了`else`，即会执行`else`结构;
- 如果执行出错，则后续代码不会继续执行，而是直接跳转至错误处理代码，即`except`语句块;
- 执行完`except`后，如果有`finally`语句块，则执行`finally`语句块;

对于`finally`语句块，如果提供了，不管有没有报错都会执行`finally`。

### 多except语句块

错误应该有很多种类，如果发生了不同类型的错误，应该由不同的except语句块处理。没错，可以有多个except来捕获不同类型的错误：

```
try:
    print('try...')
    r = 10 / int('a')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
finally:
    print('finally...')
print('END')
```

最后一个 `except` 子句可以忽略异常的名称，他将当做通配符使用。

你可以使用这种方法打印一个错误信息，然后再次把异常抛出。

```
import sys
 
try:
    f = open('myfile.txt')
    s = f.readline()
    i = int(s.strip())
except OSError as err:
    print("OS error: {0}".format(err))
except ValueError:
    print("Could not convert data to an integer.")
except:
    print("Unexpected error:", sys.exc_info()[0])
    raise
```

## 异常抛出

使用 `raise` 语句抛出异常或者指定异常：

```
try:
    print(10 / 0)
except Exception as e:
    raise ZeroDivisionError(e)
```

raise 唯一的一个参数指定了要被抛出的异常。它必须是一个异常的实例或者是异常的类（也就是 Exception 的子类）。

如果你只想知道这是否抛出了一个异常，并不想去处理它，那么一个简单的 raise 语句就可以再次把它抛出。

```
try:
    print(10 / 0)
except Exception as e:
    # 记录日志...
    raise 
```

## 小结

Python 内置的`try...except...else...finally`语法用来处理错误十分方便。当然如果有需要，我们当然可以自己定义一个错误，选择好继承关系，然后使用`raise`语句抛出错误即可。
