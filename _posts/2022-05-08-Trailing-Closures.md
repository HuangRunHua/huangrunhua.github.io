---
layout:     post                       # 使用的布局（不需要改）
title:      尾随闭包         
subtitle:   异步加载图片的新方式 
date:       2022-5-8                 # 时间
author:     Joker Hook                         # 作者
header-img: img/3.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - Swift
    - iOS
    - ImageLoader
    - SwiftUI
---

如果您需要将闭包表达式传递给函数作为函数的最终参数，并且闭包表达式很长，则将其写为尾随闭包可能是有用的。您在函数调用的括号后写一个尾随闭包，即使尾随闭包仍然是函数的参数。当您使用尾随闭包语法时，您不会将第一个闭包的参数标签作为函数调用的一部分。函数调用可以包括多个尾随闭包；然而，以下前几个示例使用单个尾随闭包。
```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // function body goes here
}

// Here's how you call this function without using a trailing closure:

someFunctionThatTakesAClosure(closure: {
    // closure's body goes here
})

// Here's how you call this function with a trailing closure instead:

someFunctionThatTakesAClosure() {
    // trailing closure's body goes here
}
```

上面的闭包表达式可以在sorted(by:)方法的括号之外写成尾随闭包：
```swift
reversedNames = names.sorted() { $0 > $1 }
```

如果闭包表达式作为函数或方法的唯一参数提供，并且您将该表达式作为尾随闭包提供，则在调用函数时，您无需在函数或方法名称后写一对括号()：
```swift
reversedNames = names.sorted { $0 > $1 }
```

当闭包足够长，无法将其内联写在一行时，尾随闭包最有用。例如，Swift的数组类型有一个`map(_:)`方法，该方法将闭包表达式作为其单个参数。为数组中的每个项目调用一次闭包，并返回该项目的替代映射值（可能是其他类型）。您通过在传递给`map(_:)`的闭包中编写代码来指定映射的性质和返回值的类型。

将提供的闭包应用于每个数组元素后，`map(_:)`方法返回一个包含所有新映射值的新数组，顺序与原始数组中的相应值相同。

以下是如何使用带有尾随闭包的`map(_:)`方法将`Int`值数组转换为字符串值数组。数组[16、58、510]用于创建新数组["OneSix", "FiveEight", "FiveOneZero"]：
```swift
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]
```

上面的代码创建了整数数字与其名称的英语版本之间的映射词典。它还定义了一个整数数数组，准备转换为字符串。

您现在可以使用数字数组创建字符串值数组，方法是将闭包表达式作为尾随闭包传递到数组的`map(_:)`方法：
```swift
let strings = numbers.map { (number) -> String in
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// strings is inferred to be of type [String]
// its value is ["OneSix", "FiveEight", "FiveOneZero"]
```

`map(_:)`方法为数组中的每个项调用闭包表达式一次。您不需要指定闭包输入参数数字的类型，因为类型可以从要映射的数组中的值中推断出来。

在本例中，变量号使用闭包数字参数的值初始化，以便可以在闭包主体内修改该值。（函数和闭包的参数始终是常量。）闭包表达式还指定字符串的返回类型，以指示将存储在映射输出数组中的类型。

闭包表达式每次调用时都会构建一个名为输出的字符串。它使用余数运算符（数字%10）计算数字的最后一个数字，并使用此数字在`digmentNames`字典中查找适当的字符串。闭包可用于创建任何大于零的整数的字符串表示形式。

> **标注**
>
> 对`diginNames`字典下标的调用后是感叹号(!)，因为字典下标返回一个可选值，表示如果键不存在，字典查找可能会失败。在上面的示例中，保证数字%10始终是`digmentNames`字典的有效下标键，因此使用感叹号来强制解开存储在下标可选返回值中的字符串值。

从`digerNames`字典中检索到的字符串被添加到输出的前面，有效地反向构建数字的字符串版本。（表达式数%10的值为16，58为8，510为0。）

然后将数字变量除以10。因为它是一个整数，所以它在分区期间四舍五入，所以16变成1，58变成5，510变成51。

重复该过程，直到数字等于0，此时输出字符串由闭包返回，并通过`map(_:)`方法添加到输出数组中。

在上述示例中使用尾随闭包语法，在闭包支持的函数之后立即整齐地封装闭包的功能，而无需将整个闭包包装在`map(_:)`方法的外括号中。

如果一个函数需要多个闭包，则省略第一个尾随闭包的参数标签，并标记剩余的尾随闭包。例如，下面的功能加载照片库的图片：
```swift
func loadPicture(from server: Server, completion: (Picture) -> Void, onFailure: () -> Void) {
    if let picture = download("photo.jpg", from: server) {
        completion(picture)
    } else {
        onFailure()
    }
}
```

当您调用此函数加载图片时，您将提供两个闭包。第一个关闭是一个完成处理程序，在成功下载后显示图片。第二个闭包是一个错误处理程序，向用户显示错误。
```swift
loadPicture(from: someServer) { picture in
    someView.currentPicture = picture
} onFailure: {
    print("Couldn't download the next picture.")
}
```

在本例中，`loadPicture(from:completion:onFailure:)`函数将其网络任务发送到后台，并在网络任务完成后调用两个完成处理程序之一。以这种方式编写该功能可以让您干净地将负责处理网络故障的代码与成功下载后更新用户界面的代码分开，而不是只使用一个处理这两种情况的闭包。

$$
x_k = Fx_{k-1} + w_{k-1}
$$
