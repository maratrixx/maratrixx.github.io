---
layout:     post
title:      "如何减小golang编译包体积"
subtitle:   ""
date:       2018-11-16 16:00:00
author:     "maratrix"
header-img: "img/home-bg-art.jpg"
tags:
    - golang
---

### 背景

golang 语言是使用静态编译，但是由 golang 编译出来的程序确实有点大，那么如何减小编译后的体积呢？

我们正常编译一个输出 `hello world` 的程序，如下：

```
package main
import "fmt"
func main() {
    fmt.Println("Hello World")
}
```

编译后，查看体积大小发现竟然有 1.9M 这么大。。。

```
go build  -o helloworld . && ll
-rwxr-xr-x  1 ******  ******   1.9M Nov 16 14:59 helloworld
```

### 减小体积

可以用下面命令，多加两个参数使其变小一些：

```
-ldflags： 表示将后面的参数传给连接器
-s：去掉符号信息
-w：去掉DWARF调试信息
```

再次编译，这次体积大小为 1.5M。
```
go build -ldflags "-s -w" -o helloworld . && ll
-rwxr-xr-x  1 ******  ******   1.5M Nov 16 15:04 helloworld
```

变小了好多，但是要注意：

```
-s：去掉符号表（这样panic时，stack trace就没有任何文件名/行号信息了，这等价于普通C/C+=程序被strip的效果）
-w：去掉DWARF调试信息，得到的程序就不能用gdb调试了
```

### 最后

> -s和-w也可以分开使用，一般来说如果不打算用gdb调试，-w基本没啥损失。
> -s的损失就有点大了。

