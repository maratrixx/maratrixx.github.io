---
layout:     post
title:      "Python 学习笔记19-文件读写"
subtitle:   ""
date:       2018-07-21 08:48:48
author:     "maratrix"
header-img: "img/contact-bg.jpg"
tags:
    - python
---

## 读文件

使用内置的 `open` 函数打开文件：

```
f = open('./data.txt', 'r')
```

如果文件不存在，open()函数就会抛出一个IOError的错误，并且给出错误码和详细的信息告诉你文件不存在：

```
>>> f = open('./aaaaaaa.txt', 'r')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: './aaaaaaa.txt'
```

### f.read() 

为了读取一个文件的内容，调用 `f.read(size)`, 这将读取一定数目的数据, 然后作为字符串或字节对象返回。

size 是一个可选的数字类型的参数。 当 size 被忽略了或者为负, 那么该文件的所有内容都将被读取并且返回。

```
>>> data = f.read()
>>> data
'hello\nworld'
```

### f.readline()

`f.readline()` 会读取一行，换行符为 `\n`。如果返回一个空字符串, 说明已经已经读取到最后一行。

```
>>> f.readline()
'hello\n'
```

### f.readlines()

`f.readlines(sizehint)` 将返回该文件中包含的所有行。

如果设置可选参数 `sizehint`, 则读取指定长度的字节, 并且将这些字节按行分割。

```
>>> f.readlines()
['hello\n', 'world']
>>> f.readlines(4)
['hello\n']
```

### 迭代每一行

```
for line in f:
    print(line, end = '')
```

最后使用`f.close()`关闭文件，文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的：

```
f.close()
```

由于文件读写时都有可能产生IOError，一旦出错，后面的`f.close()`就不会调用。所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用`try ... finally`来实现：

```
try:
    f = open(filename, 'r')
except IOError as err:
    print(err)
finally:
    if f: f.close()
```

### with 语法

每次使用 `try...finally`太繁琐了，Python引入了`with`语法来自动帮我们调用`close()`方法：

```
with open(filename, 'r') as f:
    for line in f:
        print(line, end = '')
```

## 写文件

`f.write()`方法将数据写入文件，然后返回写入的字符数。

```
with open(filename, 'w') as ff:
    con = 'hello\nworld'
    n = ff.write(con)
    print('write: ', n)
```

## 二进制文件

前面讲的默认都是读取文本文件，并且是 `UTF-8` 编码的文本文件。要读取二进制文件，比如图片、视频等等，用`'rb'`模式打开文件即可：

```
>>> f = open('./test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```

## 字符编码

要读取非 `UTF-8` 编码的文本文件，需要给`open()`函数传入`encoding`参数，例如，读取 GBK 编码的文件：

```
>>> f = open('./gbk.txt', 'r', encoding='gbk')
>>> f.read()
'测试'
```

遇到有些编码不规范的文件，你可能会遇到`UnicodeDecodeError`，因为在文本文件中可能夹杂了一些非法编码的字符。遇到这种情况，`open()`函数还接收一个`errors`参数，表示如果遇到编码错误后如何处理。最简单的方式是直接忽略：

```
>>> f = open('./gbk.txt', 'r', encoding='gbk', errors='ignore')
```

## 其他方法

### f.tell()

返回文件对象当前所处的位置, 它是从文件开头开始算起的字节数。

### f.seek()

如果要改变文件当前的位置, 可以使用 `f.seek(offset, from_what)` 函数。

`from_what` 的值, 如果是 0 表示开头, 如果是 1 表示当前位置, 2 表示文件的结尾，例如：

- seek(x,0) ： 从起始位置即文件首行首字符开始移动 x 个字符
- seek(x,1) ： 表示从当前位置往后移动 x 个字符
- seek(-x,2) ：表示从文件的结尾往前移动 x 个字符

### f.truncate([size])

从文件的首行首字符开始截断，截断文件为 size 个字符，无 size 表示从当前位置截断；截断之后后面的所有字符被删除。

### f.flush()

刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入。

## 小结

读写文件是最常见的 IO 操作。Python 内置了读写文件的函数，用法和 C 是兼容的。