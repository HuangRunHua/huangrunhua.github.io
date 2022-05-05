---
layout:     post   				    # 使用的布局（不需要改）
title:      具有隐式返回的函数 				# 标题 
subtitle:   函数返回区域不一定要有return关键字 #副标题
date:       2022-5-5 				# 时间
author:     Joker Hook 						# 作者
header-img: img/5.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
---

如果函数的整个主体都是单个表达式，则函数隐式返回该表达式。例如，以下两个函数都有相同的行为：
```swift
func greeting(for person: String) -> String {
    "Hello, " + person + "!"
}
print(greeting(for: "Dave"))
// Prints "Hello, Dave!"

func anotherGreeting(for person: String) -> String {
    return "Hello, " + person + "!"
}
print(anotherGreeting(for: "Dave"))
// Prints "Hello, Dave!
```


`Greeting(for:)`函数的整个定义是它返回的问候消息，这意味着它可以使用这种较短的形式。`anotherGreeting(for:)`函数返回相同的问候信息，像使用更长的函数一样使用返回关键字。您仅写为一条返回行的任何函数都可以省略返回。

> **标注**
>
> 您写入的隐式返回值的代码需要返回一些值。例如，您不能使用`print(13)`作为隐式返回值。但是，您可以使用永远不会像`fatalError("oh no!")`那样返回的函数作为隐式返回值，因为Swift知道隐式返回不会发生。