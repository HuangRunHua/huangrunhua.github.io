---
layout:     post                       # 使用的布局（不需要改）
title:      浅析自适应网络模糊推理系统(ANFIS)
subtitle:   模糊推断与神经网络的结合
date:       2022-5-16                 # 时间
author:     Joker Hook                         # 作者
header-img: img/6.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - adaptive network
    - fuzzy system
---

> **摘要**
> 
> 本文主要介绍分析由Jyh-Shing Roger Jang于1993年发表的开创性论文[ANFIS: adaptive-network-based fuzzy inference system](https://ieeexplore.ieee.org/abstract/document/256541)

## 模糊IF-Then准则
模糊IF-Then准则有以下两种常用的形式：
- **IF A THEN B**, 其中A与B都是**模糊用词**。例如`If pressure is high, then volume is small`，本例子中关键词`high`与`small`均为模糊的数学概念，可由人为划分界限。
- **IF A THEN C**, 其中A为模糊用词， C为精确的数学表达式。例如`If velocity is high, then force = 3.14`。

## 模糊推理系统(Fuzy Inference Systems)
标准模糊推理系统如下图所示:
![模糊推理系统](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/ANFIS/1.png)

根据定义，模糊推理系统由五个部分组成：
- 包含大量模糊if-then规则的规则库
- 定义模糊规则中使用的模糊集的隶属函数的数据库
- 决策单元，对规则执行推理操作
- 模糊化接口，将清晰的输入转换为与语言值匹配的程度
- 反模糊接口，将推理的模糊结果转换为清晰的输出

其中规则库与数据库的联合称为知识库。
