---
layout:     post                       # 使用的布局（不需要改）
title:      闭包是参考类型
subtitle:   已知一个随机向量的特性求取另一个随机向量的统计特性
date:       2022-5-13                 # 时间
author:     Joker Hook                         # 作者
header-img: img/1.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---


在[在闭包中获取值](https://huangrunhua.github.io/2022/05/09/Capturing-Values/)示例中，`incrementBySeven`和`incrementByTen`是常量，但这些常量引用的闭包仍然能够增加它们捕获的`runningTotal`变量。这是因为函数和闭包是参考类型。

每当您为常量或变量分配函数或闭包时，您实际上是在将该常量或变量设置为对函数或闭包的引用。在上面的示例中，`incrementByTen`指的是闭包的选择是常量，而不是闭包本身的内容。

这也意味着，如果您将闭包分配给两个不同的常量或变量，这两个常量或变量都引用相同的闭包。
```swift
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()
// returns a value of 50

incrementByTen()
// returns a value of 60
```

上面的示例表明，调用`incrementByTen`与调用`incrementByTen`相同。由于它们都引用相同的闭包，因此它们都会增量并返回相同的运行总数。
