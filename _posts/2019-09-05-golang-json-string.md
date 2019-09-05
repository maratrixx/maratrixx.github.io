---
layout:     post
title:      "Go采坑系列|json标准库string标签你用对了么"
subtitle:   ""
date:       2019-09-05 12:00:00
author:     "maratrix"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - golang
	- Go采坑系列

---



工作中，我们会经常用到Go自带的json标准库，使用也很简单，具体用法这里不多说。



有的时候上游传过来的字段是`string`类型的，但是我们却想用变成`int`来使用。 本来用一个`json:",string"` 就可以支持了，如果不知道golang的这些小技巧，就要大费周章了。



但是，用这个小技巧有个坑，最近踩到了，简单记录下。



首先，我们定义一个User结构体：

```
type User struct {
	Age int `json:"age,string"`
}
```

假设我们的上游json数据为：

```
{
	"age": "18"
}
```



那么，我们很方便的使用Go解析出来：

```
var u User
if err := json.Unmarshal([]byte(jsonData), &u); err != nil {
	panic(err)
}
fmt.Printf("%+v\n", u)

//输出：{Age:18}
```



如果我们拿到的上游json数据为：

```
{
	"age": ""
}
```



再次使用上面的Go代码去解析，直接就会panic报错：

```
panic: json: invalid use of ,string struct tag, trying to unmarshal "" into int
```



**所以，我们能够使用`json:",string"`特性的前提是保证上游的json数据值不能为空**



那这个问题该怎么解决呢？两种方案：



* 使用`interface{}`类型来定义
* 使用第三方库`jsoniter`来解决



第一种方案属于最「low」那种，不多说，我们说下`jsoniter`如何做到的。



首先，我们去掉`json:",string"`的标签：

```
type User struct {
	Age int `json:"age"`
}
```



然后，我们的Go程序改为：

```
var u User
json := jsoniter.ConfigCompatibleWithStandardLibrary
extra.RegisterFuzzyDecoders() 
if err := json.Unmarshal([]byte(jsonData), &u); err != nil {
panic(err)
}
fmt.Printf("%+v\n", u)
//正常输出：{Age:0}
```



其实是`extra.RegisterFuzzyDecoders() `这个方法来启动模糊模式来支持 PHP 传递过来的 JSON。



### 小结

* 使用标准库`json:",string"`时候需要保证json值不能为空
* `jsoniter`一些特性对于程序处理还是很方便的

