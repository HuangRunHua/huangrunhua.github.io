---
layout:     post   				    # 使用的布局（不需要改）
title:      函数的类型				# 标题 
subtitle:   更多有关函数的知识点 #副标题
date:       2022-5-4 				# 时间
author:     Joker Hook 						# 作者
header-img: img/7.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
    - iPadOS
    - macOS
---
每个函数都有特定的函数类型，由参数类型和函数的返回类型组成。例如：
```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

此示例定义了两个简单的数学函数，称为`addTwoInts`和`multiplyTwoInts`。这些函数各接受两个`Int`值，并返回一个`Int`值，这是执行适当数学运算的结果。

这两个函数的类型是`(Int，Int) -> Int`。这可以理解为：**具有两个参数的函数，两个参数都是Int类型，并且返回Int类型的值。**

以下是另一个例子，对于没有参数或返回值的函数：
```swift
func printHelloWorld() {
    print("hello, world")
}
```
此函数的类型是`() -> Void`，或“**没有参数并返回`Void`的函数**”。