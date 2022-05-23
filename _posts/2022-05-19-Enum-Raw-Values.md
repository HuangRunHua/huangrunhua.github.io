---
layout:     post                       # 使用的布局（不需要改）
title:      Swift 枚举元素的原始值
subtitle:   What is Raw Value in Enum
date:       2022-5-19                 # 时间
author:     Joker Hook                         # 作者
header-img: img/2.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

作为关联值的替代品，枚举情况可以预先填充默认值（称为原始值），这些值都是同一类型。

这里有一个将原始ASCII值与命名枚举案例一起存储的示例：
```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

在这里，名为`ASCIIControlCharacter`的枚举的原始值被定义为字符类型，并设置为一些更常见的`ASCII`控制字符。

原始值可以是字符串、字符或任何整数或浮点数类型。每个原始值在其枚举声明中必须是唯一的。

> **标注**
> 
> 原始值与关联值不同。当首次在代码中定义枚举时，原始值设置为预填充值，如上面的三个ASCII代码。特定枚举情况的原始值始终相同。当您根据枚举的一个情况创建新常量或变量时，会设置关联值，并且每次这样做时都可能不同。

## 隐式分配的原始值

当处理存储整数或字符串原始值的枚举时，不必为每个案例显式分配原始值。此时，Swift会自动分配值。

例如，当整数用于原始值时，每种情况下的隐式值比上一种情况多一个。如果第一个案例没有值集，则其值为0。

以下枚举是对早期行星(`Planet`)枚举的细化，其整数原始值表示每颗行星与太阳的顺序：
```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

在上面的示例中，`Planet.mercury`的显式原始值为1，`Planet.venus`的隐式原始值为2，以此类推。

当字符串用于原始值时，每个案例的隐式值是该案例名称的文本。

下面的枚举是对早期`CompassPoint`枚举的细化，字符串原始值表示每个方向的名称：
```swift
enum CompassPoint: String {
    case north, south, east, west
}
```

在上面的示例中，`CompassPoint.south`的隐式原始值为“south”，以此类推。

您可以使用其`rawValue`属性访问枚举案例的原始值：
```swift
let earthsOrder = Planet.earth.rawValue
// earthsOrder is 3

let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection is "west"
```

## 从原始值初始化

如果使用原始值类型定义枚举，枚举会自动收到一个初始化器，该初始化器接受原始值类型的值（作为称为`rawValue`的参数），并返回枚举大小写或`nil`。

此示例从原始值`7`中识别天王星：
```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet is of type Planet? and equals Planet.uranus
```

然而，并非所有可能的`Int`值都会找到匹配的行星。因此，原始值初始化器总是返回一个可选的枚举情况。在上面的示例中，`possiblePlanet`为`Planet?`或`optional Planet`。

> **标注**
>
> 原始值初始化器是一个有可能失败的初始化器，因为并非每个原始值都会返回一个枚举情况。

如果您试图找到位置为`11`的行星，原始值初始化器返回的`optional Planet`将为`nil`：
```swift
let positionToFind = 11
if let somePlanet = Planet(rawValue: positionToFind) {
    switch somePlanet {
    case .earth:
        print("Mostly harmless")
    default:
        print("Not a safe place for humans")
    }
} else {
    print("There isn't a planet at position \(positionToFind)")
}
// Prints "There isn't a planet at position 11"
```

此示例使用可选绑定来尝试访问原始值为11的行星。`if let somePlanet = Planet(rawValue：11)`创建一个`optional Planet`，如果可以检索，则将somePlanet设置为该`optional Planet`的值。在这种情况下，由于无法检索位置为11的行星，因此执行了`else`分支。
