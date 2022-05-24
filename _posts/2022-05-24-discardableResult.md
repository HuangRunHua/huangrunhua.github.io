---
layout:     post                       # 使用的布局（不需要改）
title:      Swift中的 @discardableResult 关键字
subtitle:   基于RawRepresentable协议
date:       2022-5-24                # 时间
author:     Joker Hook                         # 作者
header-img: img/4.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

> **标注**
>
> 本文翻译自[What is @discardableResult](https://sarunw.com/posts/what-is-discardableresult/)

在Swift中编写方法时，您经常遇到一些场景，在这些场景中，您有时想忽略返回值，而在其他情况下，您想知道返回值。`@discardableResult`属性允许我们启用这两种情况，而无需处理烦人的警告或强调替换。

这是Swift中的一个小功能，但一旦你了解它，你就希望知道它。

## 如何使用@discardableResult属性？
在Swift中，如果您调用返回结果的函数，但不使用它，您将收到编译器警告。
```swift
func sum(a: Int, b: Int) -> Int {
    return a + b
}

sum(a: 1, b: 2) // Result of call to 'sum(a:b:)' is unused
```

您可能会得到如下警告:
> Result of call to 'sum(a:b:)' is unused

这是一个很好的警告，因为当您调用返回结果的方法时，您不太可能执行该方法然后丢弃结果。此警告有助于我们捕获因意外遗漏消耗结果的代码而导致的错误。

`@discardableResult`属性可用于抑制“结果未使用”警告。

将`@discardableResult`应用于这样的方法可以删除该警告：
```swift
@discardableResult 
func sum(a: Int, b: Int) -> Int {
    return a + b
}
sum(a: 1, b: 2) // No longer produce the warning
```
