---
layout:     post   				    # 使用的布局（不需要改）
title:      函数参数标签和参数名称 				# 标题 
subtitle:   如何起好函数的参数名 #副标题
date:       2022-5-5 				# 时间
author:     Joker Hook 						# 作者
header-img: img/6.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
    - iPadOS
    - macOS
---

每个函数参数都有一个参数标签和一个参数名称。调用函数时使用参数标签；每个参数都写在函数调用中，前面有参数标签。参数名称用于实现函数。默认情况下，参数使用其参数名称作为参数标签。

```swift
func someFunction(firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(firstParameterName: 1, secondParameterName: 2)
```

所有参数都必须有唯一的名称。虽然多个参数可能具有相同的参数标签，但唯一的参数标签有助于使您的代码更具可读性。

## 指定参数标签
您在参数名称之前写一个参数标签，用空格分隔：

```swift
func someFunction(argumentLabel parameterName: Int) {
    // In the function body, parameterName refers to the argument value
    // for that parameter.
}
```

以下是`greet(person:)`功能的变体，该函数采用一个人的名字和家乡，并返回问候语：

```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino.
```

使用参数标签可以允许以表达式、类似句子的方式调用函数，同时仍然提供可读且意图清晰的函数体。

## 省略参数标签
如果您不想要参数的参数标签，请为该参数写一个下划线`(_)`，而不是显式参数标签。

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)
```
如果参数有参数标签，则在调用函数时必须标记参数。

## 默认参数值
您可以通过在该参数类型之后为参数分配值来定义函数中任何参数的默认值。如果定义了默认值，您可以在调用函数时省略该参数。
```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12
```

将没有默认值的参数放在函数参数列表的开头，放在具有默认值的参数之前。没有默认值的参数通常对函数的含义更重要——首先写入它们可以更容易识别正在调用相同的函数，无论是否省略任何默认参数。

## 变异参数

变分参数接受指定类型的零个或多个值。您使用变量参数来指定当调用函数时，该参数可以传递不同数量的输入值。通过在参数的类型名称后插入三个句号字符(...)来编写变量参数。传递给变分参数的值在函数的正文中作为适当类型的数组提供。

以下示例计算了任何长度的数字列表的算术平均值（也称为平均值）：
```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

一个函数可以有多个变性参数。变参数之后的第一个参数必须有一个参数标签。参数标签明确了哪些参数传递给变量参数，哪些参数传递给变量参数之后的参数。

## 输入输出参数

默认情况下，函数参数是常量。试图从该函数主体内更改函数参数的值会导致编译时错误。这意味着您不能错误地更改参数的值。如果您希望函数修改参数的值，并希望这些更改在函数调用结束后持续存在，请将该参数定义为进出参数。

您可以通过将`inout`关键字放在参数类型之前来编写输入输出参数。进出参数有一个值，该值被传递给函数，由函数修改，并从函数中传递回来以替换原始值。有关输入输出参数和相关编译器优化行为的详细讨论，请参阅输入输出参数。

您只能传递一个变量作为进出参数的参数。您不能传递常量或字面值作为参数，因为常量和文字无法修改。当您将安培数（&）作为参数传递给输入输出参数时，您将其直接放在变量名称之前，以表示函数可以修改它。

> **标注**
>
> In-out参数不能有默认值，varariadic参数不能标记为`inout`。

这是一个名为`swapTwoInts(_:_:)`的函数示例，它有两个名为`a`和`b`的输入输出整数参数：

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

`swapTwoInts(_:_:)`函数简单地将`b`的值交换为`a`，将`a`的值交换为`b`。该函数通过将`a`的值存储在名为临时`A`的临时常量中，将`b`的值分配给`a`，然后将临时`A`分配给`b`来执行此交换。

您可以调用具有`Int`类型为两个变量的`swapTwoInts(_:_:)`函数来交换其值。请注意，当传递给`swapTwoInts(_:_:)`函数时，`someInt`和`alseInt`的名称前缀为`&`：

```swift
var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3
```

上面的示例表明，`someInt`和`alseInt`的原始值被`swapTwoInts(_:_:)`函数修改，即使它们最初是在函数之外定义的。

> **标注**
>
> 输入输出参数与从函数返回值不同。上面的`swapTwoInts`示例没有定义返回类型或返回值，但它仍然修改`someInt`和`alint`的值。输入输出参数是函数在其函数体范围之外产生效果的另一种方式。