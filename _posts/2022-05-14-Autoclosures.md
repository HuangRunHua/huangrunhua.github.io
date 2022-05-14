---
layout:     post                       # 使用的布局（不需要改）
title:      自动闭包Autoclosures
subtitle:   如何省略闭包大括号与延迟闭包的执行
date:       2022-5-14                 # 时间
author:     Joker Hook                         # 作者
header-img: img/3.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
---

自动闭包是自动创建的闭包，用于包装作为参数传递给函数的表达式。它不需要任何参数，当调用它时，它会返回包裹在里面的表达式的值。这种语法便利性允许您通过编写普通表达式而不是显式闭包来省略函数参数周围的大括号。

调用采用自动关闭的函数很常见，但实现这种功能并不常见。例如，`assert(condition:message:file:line:)`函数对其条件和消息参数进行自动创建闭包；其条件参数仅在调试构建中进行计算，其消息参数仅在条件为`false`时进行计算。

自动关闭允许您延迟评估，因为在您调用关闭之前，里面的代码不会运行。延迟评估对于具有副作用或计算成本的代码非常有用，因为它允许您控制代码何时被评估。下面的代码显示了关闭如何延迟评估。
```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"

print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"
```

即使闭包内的代码删除了`customersInLine`数组的第一个元素，但在实际调用闭包之前，数组元素也不会被删除。如果从未调用闭包，则永远不会计算闭包内的表达式，这意味着永远不会删除数组元素。请注意，`customerProvider`的类型不是字符串，而是`() -> String`，一个没有返回字符串的参数的函数。

当您将闭包作为参数传递给函数时，您将获得相同的延迟求值行为。
```swift
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Prints "Now serving Alex!"
```

上面列表中的`serve(customer:)`函数采用显式闭包，返回客户姓名。下面的`serve(customer:)`执行相同的操作，但它不是采取显式闭包，而是通过用`@autoclosure`属性标记其参数的类型来进行自动闭包。现在，您可以像调用`String`参数而不是闭包一样调用该函数。该参数会自动转换为闭包，因为`customerProvider`参数的类型标有`@autoclosure`属性。
```swift
// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```
> **标注**
>
> 过度使用自动关闭可能会使您的代码难以理解。上下文和函数名称应明确表示正在推迟评估。

如果您想要一个允许转义的自动闭包，请同时使用`@autoclosure`和`@escaping`属性。
```swift
// customersInLine is ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))

print("Collected \(customerProviders.count) closures.")
// Prints "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// Prints "Now serving Barry!"
// Prints "Now serving Daniella!"
```

在上面的代码中，`collectCustomerProviders(_:)`函数将闭包附加到`customerProviders`数组中，而不是将传递给它的闭包作为其`customerProvider`参数。数组声明在函数范围之外，这意味着数组中的闭包可以在函数返回后执行。因此，必须允许客户提供商参数的值转义函数的范围。

对上面的代码进一步分析，执行如下程序：
```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
var customerProviders: [() -> String] = []

let customerProvider = { customersInLine.remove(at: 0) }

func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}

collectCustomerProviders(customerProvider())
print(customersInLine.count)
// Print "5"
collectCustomerProviders(customerProvider())
print(customersInLine.count)
// Print "5"

// 此处开始执行闭包中的删除操作
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
    print(customersInLine)
}
// Output:
//      Now serving Chris!
//      ["Alex", "Ewa", "Barry", "Daniella"]
//      Now serving Alex!
//      ["Ewa", "Barry", "Daniella"]
```

需要注意的是，在上面的代码中，当执行循环操作时才开始执行闭包中的删除操作。

