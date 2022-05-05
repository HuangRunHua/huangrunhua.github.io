---
layout:     post   				    # 使用的布局（不需要改）
title:      检查API可用性 				# 标题 
subtitle:   不是所有的API都可以在任何平台随意使用 #副标题
date:       2022-5-2 				# 时间
author:     Joker Hook 						# 作者
header-img: img/3.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
    - iPadOS
    - macOS
---
Swift内置了对检查API可用性的支持，这确保您不会意外使用给定部署目标上不可用的API。

编译器使用SDK中的可用性信息来验证代码中使用的所有API是否在项目指定的部署目标上可用。如果您尝试使用不可用的API，Swift会在编译时报告错误。

您在if或guard语句中使用可用性条件执行代码块，具体取决于您要使用的API在运行时是否可用。编译器在验证该代码块中的API是否可用时，会使用可用性条件中的信息。

```swift
if #available(iOS 12, macOS 11.3, *) {
    // Use iOS 12 APIs on iOS, and use macOS 11.3 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```

上述可用性条件指定，在iOS中，if语句的正文仅在iOS 12及更高版本中执行；在macOS中，仅在macOS 11.3及更高版本中执行。最后一个参数`*`是必需的，并指定在任何其他平台上，if的正文对目标指定的最小部署目标执行。

一般来说，可用性条件包括平台名称和版本的列表。您使用iOS、macOS、watchOS和tvOS等平台名称——完整列表请参阅声明属性。除了指定iOS 8或macOS 10.10等主要版本号外，您还可以指定iOS 11.2.6和macOS 10.13.3等次要版本号。

```swift
if #available(platform name version, ..., *) {
    // statements to execute if the APIs are available
} else {
    // fallback statements to execute if the APIs are unavailable
}
```