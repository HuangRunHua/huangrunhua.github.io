---
layout:     post                       # 使用的布局（不需要改）
title:      在闭包中获取值
subtitle:   处理闭包表达式较长的情况
date:       2022-5-9                 # 时间
author:     Joker Hook                         # 作者
header-img: img/4.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - Swift
    - iOS
    - ImageLoader
    - SwiftUI
---

闭包可以从定义它的周围上下文捕获常量和变量。然后，闭包可以从其正文中引用和修改这些常量和变量的值，即使定义常量和变量的原始范围已不复存在。

在Swift中，可以捕获值的最简单闭包形式是嵌套函数，写在另一个函数的主体中。嵌套函数可以捕获其外部函数的任何参数，也可以捕获外部函数中定义的任何常量和变量。

这里有一个名为`makeIncrementer`的函数的示例，它包含一个名为`incrementer`的嵌套函数。嵌套`incrementer()`函数从其周围的上下文中捕获两个值，运行总值和金额。捕获这些值后，`makeIncrementer`将作为闭包返回增量器，每次调用时都会按数量增加运行总量。
```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}
```

`makeIncrementer`的返回类型是`()-> Int`。这意味着它返回一个函数，而不是一个简单的值。它返回的函数没有参数，每次调用时都会返回一个`Int`值。

`makeIncrementer(forIncrement:)`函数定义了一个名为`peringTotal`的整数变量，以存储将返回的增量的当前运行总数。此变量初始化值为0。

`makeIncrementer(forIncrement:)`函数具有单个`Int`参数，参数标签为`forIncrement`，参数名称为金额。传递给此参数的参数值指定每次调用返回的增量函数时，运行总值应该增加多少。`makeIncrementer`函数定义了一个名为`incrementer`的嵌套函数，该函数执行实际增量。此函数只需为运行`Total`添加数量，然后返回结果。

当孤立地考虑时，`incrementer()`函数可能看起来不寻常：
```swift
func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
}
```

`incrementer()`函数没有任何参数，但它指的是在其函数主体内`runningTotal`和`amount`。它通过从周围函数中捕获对`runningTotal`和`amount`的引用，并在自己的函数体中使用它们来做到这一点。通过引用捕获可以确保在`makeIncrementer`调用结束时`runningTotal`和`amount`不会消失，并确保下次调用增量器函数时`runningTotal`可用。

> **注意**
>
> 作为优化，如果值没有被闭包突变，并且值在闭包创建后没有突变，则可以捕获和存储该值的副本。
> 当不再需要变量时，Swift还会处理与处理变量有关的所有内存管理。

以下是mmakeIncrementer在实际使用中的示例：
```swift
let incrementByTen = makeIncrementer(forIncrement: 10)
```

此示例设置了一个名为`incrementByTen`的常量，以引用每次调用时为其`runningTotal`变量添加10个增量函数。多次调用函数会显示此行为：
```swift
incrementByTen()
// returns a value of 10
incrementByTen()
// returns a value of 20
incrementByTen()
// returns a value of 30
```

如果您创建第二个增量器，它将对一个新的、单独的`runningTotal`变量有自己的存储引用：
```swift
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// returns a value of 7
```

调用原始增量器`(incrementByTen)`再次继续增加自己的`runningTotal`变量，并且不影响`incrementBySeven`捕获的变量：
```swift
incrementByTen()
// returns a value of 40
```

> **标注**
>
> 如果您为类实例的属性分配闭包，并且闭包通过引用实例或其成员来捕获该实例，您将在闭包和实例之间创建一个强大的引用周期。Swift使用捕获列表来打破这些强大的参考周期。
