---
layout:     post                       # 使用的布局（不需要改）
title:      使用@AppStorage存取自定义格式的列表
subtitle:   基于RawRepresentable协议
date:       2022-5-23                # 时间
author:     Joker Hook                         # 作者
header-img: img/3.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - swiftui
---

## @AppStorage简单介绍
`@AppStorage` 是 `SwiftUI` 框架提供的一个属性包装器，设计初衷是创建一种在视图中保存和读取 `UserDefaults` 变量的快捷方法。`@AppStorage` 在视图中的行为同`@State`很类似，其值变化时将导致与其依赖的视图无效并进行重新绘制。

@AppStorage 声明时需要指定在 UserDefaults 中保存的键名称（Key）以及默认值：
```swift
@AppStorage("username") var name = "fatbobman"
```

尽管`@AppStorage`是作为`UserDefaults`的属性包装器存在的，但`@AppStorage`并没有支持全部的`property list`数据类型，目前仅支持：`Bool`、`Int`、`Double`、`String`、`URL`、`Data`。

## 支持Array的@AppStorage
`@AppStorage` 支持符合`RawRepresentable`协议且`RawValue`为`Int`或`String`的数据类型。通过增加`RawRepresentable`协议的支持，可以在`@AppStorage`中读取存储原本并不支持的数据类型。

以下扩展协议实现了对`Array`的存储:
```swift
extension Array: RawRepresentable where Element: Codable {
    public init?(rawValue: String) {
        guard let data = rawValue.data(using: .utf8),
              let result = try? JSONDecoder().decode([Element].self, from: data)
        else { return nil }
        self = result
    }

    public var rawValue: String {
        guard let data = try? JSONEncoder().encode(self),
              let result = String(data: data, encoding: .utf8)
        else {
            return "[]"
        }
        return result
    }
}
```

若数组的元素为某一个结构体或类，则该结构体或类需要遵循`Codable`, 由此形成的列表便可以直接被`@AppStorage`所接受，无需再声明对该类或结构体的扩展:
```swift
enum DeviceEnum: String, CaseIterable, Identifiable, Codable {
    var id: Self { self }

    case iPhone
    case iPad
    case AppleWatch
    case LaptopComputer
    case AirPods
    case HomePod
    case TV
}

struct Device: Identifiable, Codable {
    var id = UUID()
    var device: DeviceEnum
    var image: String {
        device.rawValue.lowercased()
    }
    var isSelected: Bool = false
}
```

上述代码中`Device`结构体遵循`Codable`协议，则在创建该数组的时候便可以直接使用`@AppStorage`关键词:
```swift
@AppStorage("AllCases") var allDevices = [
    Device(device: .iPhone, isSelected: true),
    Device(device: .iPad),
    Device(device: .AppleWatch),
    Device(device: .LaptopComputer, isSelected: true),
    Device(device: .AirPods),
    Device(device: .HomePod),
    Device(device: .TV),
]
```

