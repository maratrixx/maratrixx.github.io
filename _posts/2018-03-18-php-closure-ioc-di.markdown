---
layout:     post
title:      "PHP 闭包那些事儿"
subtitle:   ""
date:       2018-03-18 12:00:00
author:     "maratrix"
header-img: "img/home-bg-art.jpg"
tags:
    - php
---

### 匿名函数
匿名函数，也叫闭包函数，说白了就是“没有名字的函数”，和一般函数结构一样，只是少了函数名以及最后需要加上分号`;`。

> 注：理论上讲闭包和匿名函数是不同的概念，不过PHP将其视作相同的概念。

```
$func = function()
{
    echo 'Hello World' . PHP_EOL;
};
$func();
```

匿名函数和普通函数的区分有：
- 匿名函数也可以作为变量的值来使用。
- **匿名函数可以从父作用域继承变量，而这个父作用域是定义该闭包的函数（不一定是调用它的函数）。**

```
$message = 'hello';
$example = function () use ($message) {
    return $message;
};
$message = 'world';
echo $example();

输出：hello
```
注意：必须使用`use`关键字将变量传递进去才行，具体见[官方文档](http://php.net/manual/zh/functions.anonymous.php)。

### 闭包类
定义一个闭包函数，其实就是实例化一个闭包类(`Closure`)对象：

```
$func = function()
{
    echo 'hello world' . PHP_EOL;
};
var_dump($func);

输出：
object(Closure)#1 (0) {
}
```

类摘要：

```
Closure {
     __construct ( void )
     public static Closure bind ( Closure $closure , object $newthis [, mixed $newscope = 'static' ] )
     public Closure bindTo ( object $newthis [, mixed $newscope = 'static' ] )
}
```
除了以上方法，闭包还实现了一个`__invoke()`魔术方法，当尝试以调用函数的方式调用一个对象时，`__invoke() `方法会被自动调用。

### bindTo 方法
接下来我们来看看`bindTo`方法，通过该方法，我们可以把闭包的内部状态绑定到其他对象上。这里`bindTo`方法的第二个参数显得尤为重要，其作用是指定绑定闭包的那个对象所属的PHP类，这样，闭包就可以在其他地方访问绑定闭包的对象中受保护和私有的成员变量。

你会发现，PHP框架经常使用`bindTo`方法把路由URL映射到匿名回调函数上，框架会把匿名回调函数绑定到应用对象上，这样在匿名函数中就可以使用`$this`关键字引用重要的应用对象：

```
class App {
    protected $routes = [];
    protected $responseStatus = '200 OK';
    protected $responseContentType = 'text/html';
    protected $responseBody = 'Hello World';

    public function addRoute($path, $callback) {
        $this->routes[$path] = $callback->bindTo($this, __CLASS__);
    }

    public function dispatch($path) {
        foreach ($this->routes as $routePath => $callback) {
            if( $routePath === $path) {
                $callback();
            }
        }
        header('HTTP/1.1 ' . $this->responseStatus);
        header('Content-Type: ' . $this->responseContentType);
        header('Content-Length: ' . mb_strlen($this->responseBody));
        echo $this->responseBody;
    }

}
```

这里我们需要重点关注`addRoute`方法，这个方法的参数分别是一个路由路径和一个路由回调，`dispatch`方法的参数是当前HTTP请求的路径，它会调用匹配的路由回调。第9行是重点所在，我们将路由回调绑定到了当前的App实例上。这么做能够在回调函数中处理App实例的状态：

```
$app = new App();
$app->addRoute(‘/user’, function(){
    $this->responseContentType = ‘application/json;charset=utf8’;
    $this->responseBody = '世界你好';
});
$app->dispatch('/user');
```

### IoC 容器
**匿名函数可以从父作用域继承变量，而这个父作用域是定义该闭包的函数（不一定是调用它的函数）。**

利用这个特性，我们可以实现一个简单的控制反转`IoC`容器：

```
class Container
{
    protected static $bindings;
 
    public static function bind($abstract, Closure $concrete)
    {
        static::$bindings[$abstract] = $concrete;
    }
 
    public static function make($abstract)
    {
        return call_user_func(static::$bindings[$abstract]);
    }
}
 
class talk
{
    public function greet($target)
    {
        echo 'Hello ' . $target->getName();
    }
}

class A
{
    public function getName()
    {
        return 'World';
    }
}
 
// 创建一个talk类的实例
$talk = new talk();
 
// 将A类绑定至容器，命名为foo
Container::bind('foo', function() {
    return new A;
});
 
// 通过容器取出实例
$talk->greet(Container::make('foo')); // Hello World
```

上述例子中，只有在通过`make`方法获取实例的时候，实例才被创建，这样使得我们可以实现容器。

在`Laravel`框架底层也大量使用了闭包以及`bindTo`方法，利用好闭包可以实现更多的高级特性如事件触发等。
