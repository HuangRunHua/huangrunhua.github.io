---
layout:     post   				    # 使用的布局（不需要改）
title:      函数的类型				# 标题 
subtitle:   更多有关函数的知识点 #副标题
date:       2022-5-4 				# 时间
author:     Joker Hook 						# 作者
header-img: img/7.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
    - iPadOS
    - macOS
---
每个函数都有特定的函数类型，由参数类型和函数的返回类型组成。例如：
```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}
```

此示例定义了两个简单的数学函数，称为`addTwoInts`和`multiplyTwoInts`。这些函数各接受两个`Int`值，并返回一个`Int`值，这是执行适当数学运算的结果。

这两个函数的类型是`(Int，Int) -> Int`。这可以理解为：**具有两个参数的函数，两个参数都是Int类型，并且返回Int类型的值。**

以下是另一个例子，对于没有参数或返回值的函数：
```swift
func printHelloWorld() {
    print("hello, world")
}
```
此函数的类型是`() -> Void`，或“**没有参数并返回`Void`的函数**”。

## 使用功能类型

您使用的功能类型就像 Swift 中的任何其他类型一样。例如，您可以将常量或变量定义为函数类型，并为该变量分配适当的函数：

```swift
var mathFunction: (Int, Int) -> Int = addTwoInts
```

这可以理解为：

“定义一个名为`mathFunction`的变量，该变量具有一种‘接受两个Int值并返回一个`Int`值的函数’。将此新变量设置为引用名为`addTwoInts`的函数。”

`addTwoInts(_:_:)`函数具有与`mathFunction`变量相同的类型，因此Swift的类型检查器允许此分配。

您现在可以调用名为`mathFunction`的分配函数：
```swift
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 5
```

具有相同匹配类型的不同函数可以分配给相同的变量，就像非函数类型一样：
```swift
mathFunction = multiplyTwoInts
print("Result: \(mathFunction(2, 3))")
// Prints "Result: 6
```

与任何其他类型一样，当您将函数分配给常量或变量时，您可以将其留给Swift来推断函数类型：
```swift
let anotherMathFunction = addTwoInts
// anotherMathFunction is inferred to be of type (Int, Int) -> Int
```

## 函数类型作为参数类型

您可以使用`(Int，Int) -> Int`等函数类型作为另一个函数的参数类型。这使您能够将函数实现的某些方面留给函数调用者在调用函数时提供。

以下是从上面打印数学函数结果的示例：
```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8
```

此示例定义了一个名为`printMathResult(_:_:_:)`的函数，它有三个参数。第一个参数称为`mathFunction`，类型为`(Int，Int) -> Int`。

当调用`printMathResult(_:_:_:)`时，它传递了`addTwoInts(_:_:)`函数以及整数值3和5。它调用值为3和5的函数，并打印8的结果。

`printMathResult(_:_:_:)`的作用是打印对适当类型的数学函数的调用结果。该函数的实现实际做什么并不重要，重要的是该函数的类型是否正确。这使`printMathResult(_:_:_:)`能够以类型安全的方式将其部分功能移交给函数的调用者。

## 函数类型作为返回类型

您可以使用函数类型作为另一个函数的返回类型。您可以通过在返回函数的返回箭头`(->)`后立即写入完整的函数类型来做到这一点。

下一个示例定义了两个简单的函数，称为`stepForward(_:)`和`stepBackward(_:)`。`stepForward(_:)`函数返回的值比其输入值多一个，`stepBackward(_:)`函数返回的值小于其输入值。这两个函数都有一种类型`(Int) -> Int`：
```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}
```

这里有一个名为`chooseStepFunction(backward:)`的函数，其返回类型为`(Int) -> Int`。`chooseStepFunction(backward:)`函数返回`stepForward(_:)`函数或`stepBackward(_:)`函数，基于称为向后的布尔参数：
```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
```

您现在可以使用`chooseStepFunction(backward:)`获取将向一个或另一个方向前进的函数：
```swift
var currentValue = 3
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function
```

上面的示例确定需要正步骤还是负步骤来逐步将名为`currentValue`的变量移动到零。`currentValue`的初始值为3，这意味着`currentValue > 0`返回`true`，导致选择`StepFunction(backward:)`返回`stepBackward(_:)`函数。对返回函数的引用存储在名为 `moveNearerToZero`的常量中。

现在，移动`NearerToZero`指的是正确的函数，它可用于计数为零：
```swift
print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

## 嵌套函数

到目前为止，您在本文中遇到的所有函数都是全局函数的示例，这些函数是在全局范围内定义的。您还可以定义其他函数主体内的函数，称为嵌套函数。

默认情况下，嵌套函数对外部世界隐藏，但仍然可以被其封闭函数调用和使用。封闭函数也可以返回其嵌套函数之一，以允许嵌套函数在另一个作用域中使用。

您可以重写上面的选择`StepFunction(backward:)`示例来使用和返回嵌套函数：
```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
var currentValue = -4
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the nested stepForward() function
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// -4...
// -3...
// -2...
// -1...
// zero!
```