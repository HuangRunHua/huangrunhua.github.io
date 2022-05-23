---
layout:     post                       # 使用的布局（不需要改）
title:      Swift中的递归枚举
subtitle:   Recursive Enumerations
date:       2022-5-22                 # 时间
author:     Joker Hook                         # 作者
header-img: img/2.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

递归枚举(`Recursive Enumerations`)是一种枚举，其枚举的另一个实例作为一个或多个枚举案例的关联值。您通过在枚举案例之前使用关键字`indirect`来指示枚举情况是递归的，这告诉编译器插入必要的间接层。

例如，这里有一个存储简单算术表达式的枚举：
```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

您还可以在枚举开始前使用关键字`indirect`，以便为所有具有相关值的枚举案例启用间接：
```swift
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

这种枚举可以存储三种算术表达式：普通数、两个表达式的加法和两个表达式的乘法。`addition`和`multiplication`具有关联值，这些值也是算术表达式(这些相关值使嵌套表达式成为可能)。例如，表达式`(5 + 4)* 2`在乘法的右侧有一个数字，在乘法的左侧有一个表达式。由于数据是嵌套的，用于存储数据的枚举也需要支持嵌套——这意味着枚举需要递归。下面的代码显示了为`(5 + 4)* 2`创建的`ArithmeticExpression`递归枚举：
```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```

递归函数是处理具有递归结构的数据的一种简单方法。例如，这里有一个计算算术表达式的函数：
```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}

print(evaluate(product))
// Prints "18"
```

此函数只需返回相关值即可计算纯数。它通过评估左侧的表达式，在右侧评估表达式，然后添加或乘以它们来计算加法或乘法。
