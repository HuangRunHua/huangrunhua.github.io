---
layout:     post                       # 使用的布局（不需要改）
title:      自适应网络模糊推理系统(ANFIS)Pytorch源码浅析
subtitle:   Pytorch for ANFIS
date:       2022-5-21                 # 时间
author:     Joker Hook                         # 作者
header-img: img/1.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - anfis
    - python
    - pytorch
---

> **标注**
>
> 本文主要分析由[James Power](http://www.cs.nuim.ie/~jpower/)开源于Github上的[anfis-pytorch](https://github.com/jfpower/anfis-pytorch)代码。

## 代码框架分析
ANFIS框架主要分为三个文件：
- `anfis.py` 定义ANFIS网络核心的部分。
- `membership.py` 该文件下定义了各种各样的隶属函数。
- `Experimental.py` 训练和测试FIS以及绘制一些图表等的实验基础设施。

程序包括一些可执行的文件
- `jang_examples.py` [Jyh-Shing Roger Jang](http://mirlab.org/jang/) 于1993年发表的开创性论文 [ANFIS: adaptive-network-based fuzzy inference system](https://ieeexplore.ieee.org/abstract/document/256541) 的仿真与实现。

## 隶属函数分析(membership.py)
本文件下定义了几类隶属函数的类模型，包括高斯隶属函数类模型(`GaussMembFunc`)与贝尔隶属函数类模型(`BellMembFunc`)等。本文主要分析高斯隶属函数类模型。
```python
class GaussMembFunc(torch.nn.Module):
    def __init__(self, mu, sigma):
        super(GaussMembFunc, self).__init__()
        self.register_parameter('mu', _mk_param(mu))
        self.register_parameter('sigma', _mk_param(sigma))

    def forward(self, x):
        val = torch.exp(-torch.pow(x - self.mu, 2) / (2 * self.sigma**2))
        return val

    def pretty(self):
        return 'GaussMembFunc {} {}'.format(self.mu, self.sigma)
```

上述代码定义了一个高斯隶属函数类模型，类继承于`torch.nn.Module`。`GaussMembFunc`的初始化需要传入两个参数均值`mu`与标准差`sigma`，这是由于若已知高斯函数均值与标准差，该分布便可以被确定下来。在初始化函数`__init__()`中存在如下两行代码:
```python
self.register_parameter('mu', _mk_param(mu))
self.register_parameter('sigma', _mk_param(sigma))
```

`register_parameter()`将一个不可训练的类型`Tensor`转换成可以训练的类型参数，并将参数与模型绑定，相当于变成了模型的一部分，成为了模型中可以根据训练进行变化的参数。注意到高斯隶属函数中均值与标准差均为前提参数，将会使用梯度下降法来进行数值更新。

`_mk_param()`函数的定义如下:
```python
def _mk_param(val):
    if isinstance(val, torch.Tensor):
        val = val.item()
    return torch.nn.Parameter(torch.tensor(val, dtype=torch.float))
```
该函数的作用在于将标量值转变为一个高斯隶属函数类模型可识别的torch参数。
