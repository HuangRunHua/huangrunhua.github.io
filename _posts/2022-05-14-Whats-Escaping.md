---
layout:     post                       # 使用的布局（不需要改）
title:      函数中的@Escaping参数
subtitle:   闭包作为函数参数
date:       2022-5-14                 # 时间
author:     Joker Hook                         # 作者
header-img: img/2.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

当闭包作为参数传递给函数时，闭包被称为转义函数，但在函数返回后调用闭包。当您声明将闭包作为其参数之一的函数时，您可以在参数类型之前编写`@escaping`，以指示允许闭包转义。

闭包可以转义的一种方法是存储在函数之外定义的变量中。例如，许多启动异步操作的函数将闭包参数作为完成处理程序。该函数在开始操作后返回，但在操作完成之前不会调用闭包——闭包需要转义，以便稍后调用。例如：
```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```

`someFunctionWithEscapingClosure(_:)`函数以闭包为参数，并将其添加到函数之外声明的数组中。如果您没有用`@escaping`标记此函数的参数，您将收到编译时错误。

通常，闭包通过在闭包正文中使用变量来隐式捕获变量，但在这种情况下，您需要显式变量。如果您想捕获`self`，请在使用它时显式写入`self`，或将`self`包含在闭包的捕获列表中。写`self`明确可以让您表达自己的意图，并提醒您确认没有参考周期。例如，在下面的代码中，传递给`someFunctionWithEscapingClosure(_:)`的闭包显式引用自显式。相比之下，传递给`someFunctionWithNonescapingClosure(_:)`的闭包是一个不可转义闭包，这意味着它可以隐式引用`self`。
```swift
func someFunctionWithNonescapingClosure(closure: () -> Void) {
    closure()
}

class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Prints "200"

completionHandlers.first?()
print(instance.x)
// Prints "100”
```

以下是`doSomething()`的一个版本，通过将其包含在闭包的捕获列表中来捕获`self`，然后隐含地引用`self`：
```swift
class SomeOtherClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { [self] in x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}
```

如果`self`是结构或枚举的实例，您可以始终隐式引用`self`。然而，当`self`是结构或枚举的实例时，转义闭包无法捕获对`self`的可变引用。结构和枚举不允许共享可变性。
```swift
struct SomeStruct {
    var x = 10
    mutating func doSomething() {
        someFunctionWithNonescapingClosure { x = 200 }  // Ok
        someFunctionWithEscapingClosure { x = 100 }     // Error
    }
}
```

上面示例中对`someFunctionWithEscapingClosure`函数的调用是一个错误，因为它位于突变方法中，因此`self`是可变的。这违反了转义闭包不能捕获结构对`self`的可变引用的规则。
