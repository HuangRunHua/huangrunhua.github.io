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

#### GaussMembFunc类模型
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

#### make_gauss_mfs函数
代码文件内还定义了一个`make_gauss_mfs`函数，用于获得多高斯隶属函数模型的情况:
```python
def make_gauss_mfs(sigma, mu_list):
    return [GaussMembFunc(mu, sigma) for mu in mu_list]
```

`make_gauss_mfs`函数参数为隶属函数的标准差(`sigma`)与均值列表(`mu_list`)，函数返回一个高斯隶属函数类模型列表。

#### make_anfis函数
当获得到隶属函数之后，便可以将隶属函数与输入变量添加到ANFIS网络同时初始化网络，`make_anfis`函数的作用便在于此。完整的`make_anfis`函数代码如下:
```python
def make_anfis(x, num_mfs=5, num_out=1, hybrid=True):
    # 获取输入量的个数
    
    num_invars = x.shape[1]
    # 沿着x每列求最大值和最小值
    
    minvals, _ = torch.min(x, dim=0)
    maxvals, _ = torch.max(x, dim=0)
    # 得到输入各个状态量的取值范围
    
    ranges = maxvals-minvals
    invars = []
    for i in range(num_invars):
        # 计算高斯隶属函数的方差
        
        sigma = ranges[i] / num_mfs
        mulist = torch.linspace(minvals[i], maxvals[i], num_mfs).tolist()
        invars.append(('x{}'.format(i), make_gauss_mfs(sigma, mulist)))
    outvars = ['y{}'.format(i) for i in range(num_out)]

    # 将 invars 和 outvars 作为参数传入 AnfisNet() 建立 ANFIS 网络
    
    model = AnfisNet('Simple classifier', invars, outvars, hybrid=hybrid)
    return model
```

`make_anfis`函数的参数为网络的输入变量`x`，隶属函数的个数`num_mfs`，网络输出量的个数`num_out`与是否使用最小二乘混合方法的标识位`hybrid`。默认情况下隶属函数个数为5个，网络为单个输出，同时网络默认使用最小二乘混合方法。

变量`invars`是存放隶属函数的标签与隶属函数类模型的列表，`invars`通过如下代码添加变量:
```python
invars.append(('x{}'.format(i), make_gauss_mfs(sigma, mulist)))
```
执行之后，`invars`列表被修改为如下形式:
```python
invars = [
            ['x0', [GaussMembFunc(), ...]], 
            ['x1', [GaussMembFunc(), ...]], ...
         ]
```

变量`outvars`是存放网络输出变量标签的列表，其内部形式为:
```python
outvars = ['y0', 'y1', ...]
```

`make_anfis`函数的输出为ANFIS网络，网络通过如下代码创建:
```python
model = AnfisNet('Simple classifier', invars, outvars, hybrid=hybrid)
```

## 网络层分析(anfis.py)
该文件下定义了ANFIS网络的层结构。

#### AnfisNet(torch.nn.Module)类模型
`AnfisNet(torch.nn.Module)`定义了一个完整的自适应模糊推理。其类初始化方法为:
```python
def __init__(self, description, invardefs, outvarnames, hybrid=True):
        super(AnfisNet, self).__init__()
        self.description = description
        self.outvarnames = outvarnames
        self.hybrid = hybrid
        varnames = [v for v, _ in invardefs]
        mfdefs = [FuzzifyVariable(mfs) for _, mfs in invardefs]
        self.num_in = len(invardefs)
        self.num_rules = np.prod([len(mfs) for _, mfs in invardefs])
        if self.hybrid:
            cl = ConsequentLayer(self.num_in, self.num_rules, self.num_out)
        else:
            cl = PlainConsequentLayer(self.num_in, self.num_rules, self.num_out)
        self.layer = torch.nn.ModuleDict(OrderedDict([
            ('fuzzify', FuzzifyLayer(mfdefs, varnames)),
            ('rules', AntecedentLayer(mfdefs)),
            ('consequent', cl),
            ]))
```
类初始化接受的参数一共有四个：
- `description`: 描述语句, 如'Simple classifier'
- `invardefs`: 输入变量与隶属函数列表, 如: ```invardefs = [['x0', [GaussMembFunc(), ...]], ...]```
- `outvarnames`: 网络输出结果, 如: ```outvarsnames = ['y0', 'y1', ...]```
- `hybrid`: 是否使用混合最小二乘法

`AnfisNet`类网络成员变量定义如下:
- `description`: 描述语句
- `outvarnames`: 网络输出变量名
- `hybrid`: 是否使用混合最小二乘法
- `num_in`: 网络输入变量个数
- `num_rules`: 触发强度的个数/第二层元素的个数
- `layer`: 网络的所有层

初始化阶段将传入的输入变量与隶属函数列表`invardefs`进行处理，设
```python
`invardefs` = [
    ['x0', [mf(), mf(), ...]],
    ['x1', [mf(), mf(), ...]],
    ...
]
```
则先将变量名提取出来:
```python
varnames = [v for v, _ in invardefs]
```
再将隶属函数传入模糊变量类模型内形成列表:
```ptyhon
mfdefs = [FuzzifyVariable(mfs) for _, mfs in invardefs]
```
网络输入变量名`varnames`与模糊变量类模型列表`mfdefs`作为输入变量传入网络第一层`FuzzifyLayer`与第二层`AntecedentLayer`:
```python
FuzzifyLayer(mfdefs, varnames)
AntecedentLayer(mfdefs)
```

由于ANFIS网络中第二层与第三层之间需要进行触发强度的计算，多个输入变量的隶属函数两两之间需要进行乘法运算，因此类成员变量`num_rules`存储总共需要计算的次数:
```python
self.num_rules = np.prod([len(mfs) for _, mfs in invardefs])
```

例如，
```python
invardefs = [
    ['x0', ['f1', 'f2', 'f3']],
    ['x1', ['f4', 'f5']],
]
```
则`self.num_rules = 6`

类成员变量`self.hybrid`用于判断网络是否使用混合最小二乘法，若使用则网络第四层为`ConsequentLayer`，否则为`PlainConsequentLayer`.

类成员变量`self.layer`定义了完整的网络内部层结构，为顺序的字典，可以通过键来进行索引。
```python
self.layer = torch.nn.ModuleDict(OrderedDict([
    ('fuzzify', FuzzifyLayer(mfdefs, varnames)),
    ('rules', AntecedentLayer(mfdefs)),
    ('consequent', cl),
    ]))
```

第四层网络的系数可以通过如下方式获取:
```python
self.layer['consequent'].coeff
```

由于第四层网络需要使用前向传播或混合最小二乘法来更新，因此ANFIS网络中定义了两个类方法来实现系数的更新:
```python
@coeff.setter
def coeff(self, new_coeff):
    self.layer['consequent'].coeff = new_coeff

def fit_coeff(self, x, y_actual):
    if self.hybrid:
        self(x)
        self.layer['consequent'].fit_coeff(x, self.weights, y_actual)
```

ANFIS网络的前向传播部分如下:
```python
def forward(self, x):
    self.fuzzified = self.layer['fuzzify'](x)
    self.raw_weights = self.layer['rules'](self.fuzzified)
    self.weights = F.normalize(self.raw_weights, p=1, dim=1)
    self.rule_tsk = self.layer['consequent'](x)
    y_pred = torch.bmm(self.rule_tsk, self.weights.unsqueeze(2))
    self.y_pred = y_pred.squeeze(2)
    return self.y_pred
```


