---
layout:     post                       # 使用的布局（不需要改）
title:      Enum中的关联值
subtitle:   闭包作为函数参数
date:       2022-5-15                 # 时间
author:     Joker Hook                         # 作者
header-img: img/4.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

集合中的枚举变量设置常量或变量，稍后检查此值。然而，能够将其他类型的值与这些大小写值一起存储有时是有用的。此附加信息称为关联值，每次您将该情况用作代码中的值时，它都会有所不同。

您可以定义`Swift`枚举来存储任何给定类型的关联值，如果需要，每个枚举情况下的值类型可能不同。类似的枚举被称为歧视联合(discriminated unions)、标记联合(tagged unions)或其他编程语言的变体(variants)。

例如，假设库存跟踪系统需要通过两种不同类型的条形码跟踪产品。一些产品以UPC格式标有1D条形码，使用数字`0`到`9`。每个条形码都有一个数字系统数字，然后是五个制造商代码数字和五个产品代码数字。后面跟着一个检查数字，以验证代码是否已正确扫描，例如：`8 85909 51226 3`。

其他产品以二维码格式标有二维条形码，可以使用任何ISO 8859-1字符，并可以编码长达2953个字符的字符串。

库存跟踪系统可以方便地将UPC条形码存储为四个整数的元组，并将二维码条形码存储为任何长度的字符串。

在Swift中，用于定义任一类型的产品条形码的枚举可能如下所示：
```swift
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
```

这可以理解为：

**为名为`Barcode`的枚举类型进行命名，该枚举类型可以采用具有类型关联值`(Int、Int、Int、Int)`的upc值，也可以取具有`String`类型关联值的`qrCode`值。**

此定义不提供任何实际的`Int`或`String`值——它只是定义了`Barcode`常量和变量等同于`Barcode.upc`或`Barcode.qrCode`时可以存储的关联值类型。

然后，您可以使用任一类型创建新条形码：
```swift
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
```

示例创建了一个名为`productBarcode`的新变量，并为其分配了一个`Barcode.upc`的值，其关联的元组值为`(8，85909，51226，3)`。

您可以为同一产品分配不同类型的条形码：
```swift
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")
```

此时，原始`Barcode.upc`及其整数值将被新的`Barcode.qrCode`及其字符串值取代。`Barcode`类型的常量和变量可以存储`.upc`或`.qrCode`（及其相关值），但它们在任何给定时间只能存储其中之一。

您可以使用开关语句检查不同的条形码类型。然而，这一次，关联值作为开关语句的一部分提取。您可以将每个关联值提取为常量（带有`let`前缀）或变量（带有`var`前缀），以便在`switch`的正文中使用：
```swift
switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

如果枚举大小写的所有关联值都作为常量提取，或者如果所有相关值都作为变量提取，为了简洁起义，您可以在大小写名称之前放置单个`var`或添加`let`：
```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```
