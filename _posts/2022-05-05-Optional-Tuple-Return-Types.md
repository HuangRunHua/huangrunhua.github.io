---
layout:     post   				    # 使用的布局（不需要改）
title:      可选的元组返回类型 				# 标题 
subtitle:   二〇二二年五月五日 星期四 #副标题
date:       2022-5-5 				# 时间
author:     Joker Hook 						# 作者
header-img: img/4.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
---

如果要从函数返回的元组类型有可能对整个元组具有“无值”，您可以使用可选的元组返回类型来反映整个元组可以为`nil`的事实。您通过在元组类型的结束括号后放置问号来编写可选的元组返回类型，例如`(Int，Int)?`或者`(String，Int，Bool)?`

> **标注**
>
> 可选的元组类型，如`(Int，Int)?`不同于包含可选类型的元组，如`(Int?，Int?)`。对于可选的元组类型，整个元组是可选的，而不仅仅是元组中的每个单个值。

文章[具有多个参数返回值的函数](https://huangrunhua.github.io/2022/05/05/Functions-Multiple-Return-Values/)中定义了函数`minMax(array:)`，该函数返回一个包含两个Int值的元组。然而，该功能不会对传递的数组进行任何安全检查。如果数组参数包含空数组，则定义的`minMax(array:)`函数将在尝试访问数组[0]时触发运行时错误。

要安全地处理空数组，请使用可选的元组返回类型编写`minMax(array:)`函数，并在数组为空时返回零值：
```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

您可以使用可选绑定来检查此版本的`minMax(array:)`函数是返回实际元组值还是`nil`：
```swift
if let bounds = minMax(array: [8, -6, 2, 109, 3, 71]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// Prints "min is -6 and max is 109
```