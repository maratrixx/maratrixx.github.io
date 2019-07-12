---
layout:     post
title:      "go:linkname用法"
subtitle:   ""
date:       2019-07-12 12:00:00
author:     "maratrix"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - golang

---

## 什么是`go:linkname`

这里引用[Go官方文档](https://golang.org/cmd/compile/)的解释：

> //go:linkname localname importpath.name
>
> The //go:linkname directive instructs the compiler to use “importpath.name” as the object file symbol name for the variable or function declared as “localname” in the source code. Because this directive can subvert the type system and package modularity, it is only enabled in files that have imported "unsafe".



翻译过来就是：

> 这个指令告诉编译器为函数或者变量`localname`使用`importpath.name`作为目标文件的符号名。因为这个指令破坏了类型系统和包的模块化，所以它只能在 import "unsafe" 的情况下才能使用。



只看文档有没有云里雾里？我们来看标准库`time.Sleep`代码实例：

```
// Sleep pauses the current goroutine for at least the duration d.
// A negative or zero duration causes Sleep to return immediately.
func Sleep(d Duration)

```

这个函数我们经常会用到，但是却没有函数体，而且同样在当前目录下也没有汇编语言的代码实现。那么，在哪里实现的呢？

实际上，这个函数在Go也是在标准库中实现的，它是runtime库中的一个unexported（未导出）函数。

```
// timeSleep puts the current goroutine to sleep for at least ns nanoseconds.
//go:linkname timeSleep time.Sleep
func timeSleep(ns int64) {
  ...
}
```

什么？未导出的函数竟然可以被外部访问？是的，这里要归功于`go:linkname`这个命令了。



## 如何用`go:linkname`

我们写一个简单的代码来说明如何使用`go:linkname`，代码结构如下：

```
linkname
    ├── aa
    │   ├── aa.go
    │   └── aa.s
    ├── bb
    │   └── bb.go
    └── main.go
```



首先，我们编写`aa.go`文件：

```
package aa

import _ "linkname/bb"

func Hello() string
```

这里有一点有注意：

- 必须引入`linkname/bb`包；



然后，`bb.go`文件：

```
package bb

import _ "unsafe"

//go:linkname hello linkname/aa.Hello
func hello() string {
	return "hello world"
}
```

这里有两点注意：

- 必须要引入`unsafe`包；
- 使用`go:linkname`命令指定目标变量；



最后，`main`文件：

```
package main

import (
	"fmt"
	"linkname/aa"
)

func main() {
	fmt.Println(aa.Hello())
}
```



如果此时运行`go run main.go`肯定会报错：

```
linkname/aa/aa.go:5:6: missing function body
```

难道我们前面讲的都是错的吗？

这里有一个技巧，你在` package aa`下创建一个空的文件， 文件名随意，只要文件后缀为`.s`，再运行一下`go run main.go`：

```
hello world
```

原因在于Go在编译的时候会启用`-complete`编译器flag，它要求所有的函数必需包含函数体。创建一个空的汇编语言文件绕过这个限制。



## 小结

- `go:linkname`可以跨包使用；
- 跨包使用时，目标方法或者变量必须导入有方法体的包，这个编译器才可以识别到链接 `import _ "linkname/bb"`；
- 跨包使用时，方法体的实现包必须引发`unsafe`包；
- `go build`无法编译`go:linkname`,必须用单独的compile命令进行编译，因为`go build`会加上`-complete`参数，这个参数会检查到没有方法体的方法，并且不通过。



一般情况下我们不会采用这种方式，只有在很稀少的情况下，为了更好地组织我们的代码，我们才会有选择的采采用。至少，作为一个Go开发者，我们得明白通过这种方式可以绕过权限的限制。



#####  参考博客：

[突破限制,访问其它Go package中的私有函数](https://colobu.com/2017/05/12/call-private-functions-in-other-packages/)

