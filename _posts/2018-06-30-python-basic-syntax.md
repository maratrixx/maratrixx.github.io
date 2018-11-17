---
layout:     post
title:      "Python学习笔记01-基本语法"
subtitle:   ""
date:       2018-06-30 15:10:39
author:     "maratrix"
header-img: "img/home-bg.jpg"
tags:
    - python
---


## 编码

默认情况下，python3 源码文件是以 utf-8 编码的，所有的字符串都是 unicode 字符串，当然也可以指定不同的编码

```
# -*- coding:utf-8 -*-
或者
# coding=utf-8
或者
# coding:utf-8
或者
# vim: set fileencoding=utf-8 :
```

注意:
- 必须将编码注释放在第一行或者第二行
- 有以上可选格式


不过一般使用`# -*- coding:utf-8 -*-`格式，这样写可以支持多种编辑器，移植性好。

## 标识符

和其他语言如php、go差不多。

- 第一个字符为字母或者`_`下划线
- 其他字符由字母、下划线、数字组成
- 对大小写敏感

>**在 python3 中， 支持非 ascii 标识符了。**

## 保留字

保留字即关键字，不能被使用做任何标识符。我们可以处通过标准库里面的 keyword 模块来查看：

```
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

## 注释

单行注释使用`#`开头：

```
# 这是一行注释...
```

多行注释可以用多个`#`或者使用`''' or """`:

```
# 第一个注释
# 第二个注释

'''
第三行注释
第四行注释
'''

"""
第五行注释
第六行注释
"""
```

文档型注释

```
def hello():
    '''这里是文档'''
    pass

print(hello.__doc__)
```

## 行与缩进

python 最具特色的就是使用缩进来表示代码块，不需要使用大括号 `{}` 。

缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数。

```
if True:
    print('True')
else:
    print('False')
```

以下代码最后一行语句缩进数的空格数不一致，会导致运行错误：

```
if True:
    print ("Answer")
    print ("True")
else:
    print ("Answer")
  print ("False")    # 缩进不一致，会导致运行错误
```

## 多行语句

Python 通常是一行写完一条语句，但如果语句很长，我们可以使用反斜杠 `\` 来实现多行语句，例如：

```
total = item_one + \
        item_two + \
        item_three
```

在 `[], {}, ()` 中的多行语句，不需要使用反斜杠 `\`，例如：

```
total = ['item_one', 'item_two', 'item_three',
        'item_four', 'item_five']
```

## 同一行显示多条语句

python 可以再同一行中使用多条语句，语句之间使用分号`;`分割开就行：

```
name = 'I\'m maratrix'; print(name)
```

## 代码组

缩进相同的一组语句构成一个代码块，我们称之代码组。

像 `if、while、def 和 class` 这样的复合语句，首行以关键字开始，以冒号 `:` 结束，该行之后的一行或多行代码构成代码组。

## import 与 from...import

在 python 用 `import` 或者 `from...import` 来导入相应的模块。

- 将整个模块 (somemodule) 导入，格式为： `import somemodule`

- 从某个模块中导入某个函数, 格式为： `from somemodule import somefunction`

- 从某个模块中导入多个函数, 格式为： `from somemodule import firstfunc, secondfunc, thirdfunc`

- 将某个模块中的全部函数导入，格式为： `from somemodule import *`

导入 sys 模块：

```
import sys
print (sys.argv, sys.path)
```

导入 sys 模块里面的 argv、path 函数：

```
from sys import argv, path
print(argv, path)
```

## 变量

python 中的变量不需要声明，每个变量在使用前都必须赋值，变量赋值后才会被创建。

在 python 中，变量就是变量，本身没有类型，我们所说的“类型”是变量所指向的内存中对象的类型。

## 多变量并行赋值

python 中允许你同时为几个变量同时赋值：

```
a, b, c = 123
```

或者，也可以为多个对象指定多个变量：

```
a, b, c = 1, 2, 3
```

## `#!/usr/bin/python` 与 `#!/usr/bin/env python` 的区别

python 脚本的第一行通常会写成如下形式：

```
/usr/bin/python
```

或者

```
/usr/bin/env python
```

它们用于指定执行该脚本的解释器，如上即是指定 python 作为解释器。在计算机科学中这有个专门的术语，叫做 shebang。

> 在计算机科学中，Shebang 是一个由井号和叹号构成的字符串行，其出现在文本文件的第一行的前两个字符。 在文件中存在 Shebang 的情况下，类 Unix 操作系统的程序载入器会分析 Shebang 后的内容，将这些内容作为解释器指令，并调用该指令，并将载有 Shebang 的文件路径作为该解释器的参数。

那以上两种写法到底有啥区别呢？

如果我们直接使用`python 脚本.py`方式执行的话，有内有 shebang 就无所谓了，因为我们自己指定了该脚本的解释器。

如果我们要以`./脚本.py`方式执行的话，两种写法就有区别了。

通常我们认为 `#!/usr/bin/python` 采用了绝对路径的写法，即指定了采用 `/usr/bin/python` 解释器来执行该脚本。一般类 Unix 系统下，python 解释器都位于该路径，不幸的是如果 python 解释器不在该路径下的话就无法运行。

`#!/usr/bin/env python` 的写法指定从 PATH 环境变量中来查找 python 解释器的位置，因此只要环境变量中存在，该脚本即可执行。

所以，一般情况下采用 `#!/usr/bin/env python` 的写法更好，更加具有通用性。


## 小结

- 采用缩进的方式组织代码结构，比较简洁、优雅
- 程序是大小写敏感的，写错了大小写，程序会报错
- 允许进行多变量并行赋值
- 推荐使用 `#!/usr/bin/env python` 方式来指定解释器