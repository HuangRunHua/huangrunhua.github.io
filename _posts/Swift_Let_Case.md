---
layout:     post   				    # 使用的布局（不需要改）
title:      在Swift中使用let获取模式匹配的值 				# 标题 
subtitle:   二〇二二年四月二十九日 星期五 #副标题
date:       2022-04-29 				# 时间
author:     Joker Hook 						# 作者
header-img: img/1.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
---

`Switches`支持任何类型的数据和各种各样的比较操作——它们不限于整数和等式测试。
```swift
import Foundation

let vegetable = "red pepper"

switch vegetable {
    case "celery":
        print("Add some raisins and make ants on a log.")
    case "cucumber", "watercress":
        print("That would make a good tea sandwich.")
    case let x where x.hasSuffix("pepper"):
        print("Is it a spicy \(x)?")
    default:
        print("Everything tastes good in soup.")
}
```

注意如何在模式中使用let将与模式匹配的值分配给常量。

在匹配的交换机盒中执行代码后，程序将退出交换机语句。执行不会持续到下一个案例，因此您不需要在每个案例代码末尾显式突破开关。