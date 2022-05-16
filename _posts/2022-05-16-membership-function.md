---
layout:     post                       # 使用的布局（不需要改）
title:      隶属函数Membership Function
subtitle:   为枚举类型设定类型关联值
date:       2022-5-16                 # 时间
author:     Joker Hook                         # 作者
header-img: img/5.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - function
---

## 定义
隶属函数（membership function）也称为**归属函数**或**模糊元函数**，是表征某一元素是否属于某一集合的模糊概念，与指示函数类似。指示函数定义一个集合中的元素是否属于特定子集合。一元素的指示函数的值可能是0或是1，而一元素的隶属函数会是0到1之间的数值，表示元素属于某模糊集合的`真实程度`（degree of truth）。

#### 例子
例如质数为一集合，整数3属于质数，其指示函数为1，整数4不属于质数，其指示函数为0。但针对模糊集合，可能不会有如此明确的定义，假设胖子是模糊集合，可能体重80公斤的人其隶属函数为0.9，体重70公斤的人其隶属函数为0.8。

> **标注**
>
> 隶属函数数值是在0到1之间，看似类似几率，但两者是不同的概念。

## 常用模糊隶属度函数

#### 偏小型

$$
A(x)=\left\{\begin{array}{cc}
1, & x<a \\
\frac{b-x}{b-a}, & a \leq x \leq b \\
0, & b<x
\end{array}\right.
$$

#### 中间型

$$
A(x)=\left\{\begin{array}{cc}
\frac{x-a}{b-a}, & a \leq x<b \\
1, & b \leq x<c \\
\frac{d-x}{d-c}, & c \leq x \leq d \\
0, & x<a \text { or } d<x
\end{array}\right.
$$

#### 偏大型

$$
A(x)=\left\{\begin{array}{cc}
0, & x<a \\
\frac{x-a}{b-a}, & a \leq x \leq b \\
1, & b<x
\end{array}\right.
$$

图像依次如下:
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/MembershipFunction/1.png)
