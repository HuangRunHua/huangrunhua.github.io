---
layout:     post   				    # 使用的布局（不需要改）
title:      具有多个参数返回值的函数 				# 标题 
subtitle:   元组的妙用 #副标题
date:       2022-5-5 				# 时间
author:     Joker Hook 						# 作者
header-img: img/2.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
---

Swift支持使用元组类型作为函数返回多个值作为复合返回值的一部分的返回类型。

下面的示例定义了一个名为`minMax(array:)`的函数，该函数在`Int`值数组中找到最小和最大的数字：

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
```

`minMax(array:)`函数返回一个包含两个`Int`值的元组。这些值被标记为min和max，因此在查询函数的返回值时可以通过名称访问它们。

`minMax(array:)`函数的主体首先将两个工作变量设置为数组中第一个整数的值，称为`currentMin`和`currentMax`。然后，该函数迭代数组中的剩余值，并检查每个值，看看它分别小于或大于`currentMin`和`currentMax`的值。最后，整体最小值和最大值作为两个`Int`值的元组返回。

由于元组的成员值被命名为函数返回类型的一部分，因此可以使用点语法访问它们，以检索找到的最小值和最大值：

```swift
let bounds = minMax(array: [8, -6, 2, 109, 3, 71])
print("min is \(bounds.min) and max is \(bounds.max)")
```

> 请注意，元组的成员不需要在从函数返回元组时命名，因为它们的名称已被指定为函数返回类型的一部分。