---
layout:     post                       # 使用的布局（不需要改）
title:      Swift 中的 Assertions 与 Preconditions
subtitle:   错误预处理的两种方法
date:       2022-6-16                # 时间
author:     Joker Hook                         # 作者
header-img: img/7.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - error handle
---

# Swift 中的 Assertions 与 Preconditions
`Swift` 为开发人员提供了两种错误预处理的解决方案：`assert(::file:line:)` 与 `precondition(::file:line:)`。两种解决方案的不同点在于函数 `assert(::file:line:)` 只会在 debug 模式下诊断错误，而函数 `precondition(::file:line:)` 可以在任意模式下诊断错误。

## 使用 Assertions 进行调试

通过从 `Swift` 标准库调用  `assert(_:_:file:line:)` 函数来编写断言。可以将此函数传递一个计算为true或false的表达式，如果条件的结果为false，则将显示一条消息。例如：
```swift
let age = -3
assert(age >= 0, "A person's age can't be less than zero.")
// This assertion fails because -3 isn't >= 0.
```

`Swift`允许省略断言消息——例如，当它只是作为散文重复条件时。
```swift
assert(age >= 0)
```

如果代码已经检查了条件，则使用 `assertionFailure(_:file:line:)` 函数来指示断言失败。例如：
```swift
if age > 10 {
    print("You can ride the roller-coaster or the ferris wheel.")
} else if age >= 0 {
    print("You can ride the ferris wheel.")
} else {
    assertionFailure("A person's age can't be less than zero.")
}
```

## 强制执行先決条件

通过调用 `precondition(_:_:file:line:)` 函数编写先决条件。
```swift
// In the implementation of a subscript...
precondition(index > 0, "Index must be greater than zero.")
```

还可以调用 `preconditionFailure(_:file:line:)` 函数来指示发生了故障——例如，如果采用了交换机的默认情况，但所有有效的输入数据都应该由交换机的其他情况之一处理。




