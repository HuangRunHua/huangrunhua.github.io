---
layout:     post                       # 使用的布局（不需要改）
title:      MATLAB 中的ANFIS函数使用范例
subtitle:   使用训练数据调整Sugeno型模糊推理系统
date:       2022-5-25                # 时间
author:     Joker Hook                         # 作者
header-img: img/5.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - MATLAB
    - anfis
---
 
> **标注**
>
> 本文档翻译自[anfis](https://ww2.mathworks.cn/help/fuzzy/anfis.html)

## 语法
MATLAB中`anfis`函数的语法如下:
```matlab
fis = anfis(trainingData)
fis = anfis(trainingData,options)
[fis,trainError] = anfis(___)
[fis,trainError,stepSize] = anfis(___)
[fis,trainError,stepSize,chkFIS,chkError] = anfis(trainingData,options)
```

## 函数描述
#### fis = anfis(trainingData)
`fis = anfis(trainingData)`生成单输出Sugeno模糊推理系统(FIS)，并使用指定的输入/输出训练数据调整系统参数。FIS对象使用网格分区自动生成。

训练算法使用最小二乘和反向传播梯度下降方法的组合来建模训练数据集。

#### fis = anfis(trainingData，options)
`fis = anfis(trainingData，options)`使用指定的训练数据和选项调整FIS。使用此语法，您可以指定：
- 要调谐的初始FIS对象。
- 用于防止过度拟合训练数据的验证数据。
- 训练算法选项。
- 是否显示训练进度信息。

#### [fis,trainError] = anfis()
`[fis,trainError] = anfis()`返回每个训练历元的均方根训练误差。

#### [fis,trainError,stepSize] = anfis()
`[fis,trainError,stepSize] = anfis()`返回每个训练历元的训练步长。

#### [fis,trainError,stepSize,chkFIS,chkError] = anfis(trainingData,options)
`[fis,trainError,stepSize,chkFIS,chkError] = anfis(trainingData,options)`返回每个训练历元的验证数据错误`chkError`和验证错误最小的优化FIS对象`chkFIS`。要使用此语法，必须使用选项指定验证数据。验证数据。

## 示例
### 用ANFIS训练模糊推理系统
使用如下语句加载训练数据，此数据具有单个输入和单个输出:
```matlab
load fuzex1trnData.dat
```

生成并训练模糊推理系统。默认情况下，FIS结构是使用具有两个成员函数的输入变量范围的网格分区创建的:
```matlab
fis = anfis(fuzex1trnData);
```
则生成的训练结果如下:
```bash
ANFIS info:
    Number of nodes: 12
    Number of linear parameters: 4
    Number of nonlinear parameters: 6
    Total number of parameters: 10
    Number of training data pairs: 25
    Number of checking data pairs: 0
    Number of fuzzy rules: 2


Start training ANFIS ...

1      0.229709
2      0.22896
3      0.228265
4      0.227624
Step size increases to 0.011000 after epoch 5.
5      0.227036
6      0.2265
7      0.225968
8      0.225488
Step size increases to 0.012100 after epoch 9.
9      0.225052
10      0.22465

Designated epoch number reached. ANFIS training completed at epoch 10.

Minimal training RMSE = 0.22465
```

绘制ANFIS输出和训练数据:
```matlab
x = fuzex1trnData(:,1);
anfisOutput = evalfis(fis,x);
plot(x,fuzex1trnData(:,2),'*r',x,anfisOutput,'.b')
legend('Training Data','ANFIS Output','Location','NorthWest')
```

ANFIS数据与训练数据不匹配。要改进匹配，请执行以下操作：
- 将FIS结构中的成员函数数增加到4个, 这样做会为系统添加模糊规则和可调参数。
- 增加训练次数。

```matlab
opt = anfisOptions('InitialFIS',4,'EpochNumber',40);
```

抑制错误和步长命令窗口显示:
```matlab
opt.DisplayErrorValues = 0;
opt.DisplayStepSize = 0;
```

训练FIS
```matlab
fis = anfis(fuzex1trnData,opt);
```

训练信息如下:
```bash
ANFIS info:
    Number of nodes: 20
    Number of linear parameters: 8
    Number of nonlinear parameters: 12
    Total number of parameters: 20
    Number of training data pairs: 25
    Number of checking data pairs: 0
    Number of fuzzy rules: 4

Minimal training RMSE = 0.0833853
```

此时训练数据与ANFIS输出之间的匹配得到了改善。

### 为ANFIS训练创建初始FIS
本示例创建一个单输入单输出的FIS系统。训练数据创建如下:
```matlab
x = (0:0.1:10)';
y = sin(2*x)./exp(x/5);
```

定义具有五个高斯输入隶属度函数的初始FIS结构：
```matlab
genOpt = genfisOptions('GridPartition');
genOpt.NumMembershipFunctions = 5;
genOpt.InputMembershipFunctionType = 'gaussmf';
inFIS = genfis(x,y,genOpt);
```

配置ANFIS培训选项。设置初始FIS，并取消显示训练进度：
```matlab
opt = anfisOptions('InitialFIS',inFIS);
opt.DisplayANFISInformation = 0;
opt.DisplayErrorValues = 0;
opt.DisplayStepSize = 0;
opt.DisplayFinalResults = 0;
```

使用指定选项训练FIS:
```matlab
outFIS = anfis([x y],opt);
```

将ANFIS输出与训练数据进行比较：
```matlab
plot(x,y,x,evalfis(outFIS,x))
legend('Training Data','ANFIS Output')
```
![ANFIS输出与训练数据进行比较](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/MatlabANFIS/1.png)

### 获取ANFIS训练误差
可以在训练过程使用如下语句获取训练误差:
```matlab
[fis,trainError] = anfis([x y],opt);
```

`trainError`包含每个训练历元的训练数据的均方根误差。`fis`的训练误差是`trainError`中的最小值，通过如下语句获取:
```matlab
fisRMSE = min(trainError)
```

### 获取ANFIS步长配置文件
步长增长率越大，训练收敛速度越快。然而，过多地增加步长增长率会导致收敛性差。对于本例，请尝试将步长增加率加倍。
```matlab
opt.StepSizeIncreaseRate = 2*opt.StepSizeIncreaseRate;
```
训练FIS，并返回步长数组:
```matlab
[fis,~,stepSize] = anfis([x y],opt);
```

绘制步长配置文件。最佳步长曲线应在开始时增大，达到最大值，然后在其余的训练中减小。

### 验证ANFIS网络
要验证网络的可靠性可通过如下语句获取, 其中`fuzex1chkData`为验证集:
```matlab
opt.ValidationData = fuzex1chkData;
```

训练FIS，并返回验证结果:
```matlab
[fis,trainError,stepSize,chkFIS,chkError] = anfis(fuzex1trnData,opt);
```

`training error`、`trainError`和`validation error`、`chkError`数组每个训练历元都包含一个错误值。绘制训练错误和验证错误。
```matlab
x = [1:30];
plot(x,trainError,'.b',x,chkError,'*r')
```
![](https://ww2.mathworks.cn/help/examples/fuzzy/win64/ValidateANFISTrainingExample_01.png)

最小验证错误出现在第17个历元。在这一点之后，验证误差的增加表明模型参数与训练数据过度拟合。因此，第17纪元的调谐FIS chkFIS具有最佳的泛化性能。
