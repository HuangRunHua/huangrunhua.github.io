---
layout:     post                       # 使用的布局（不需要改）
title:      在Xcode中调试App Extension程序
subtitle:   Debug for App Extensions
date:       2022-5-31                # 时间
author:     Joker Hook                         # 作者
header-img: img/7.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - xcode
---

Xcode默认情况下没有为开发人员提供直接启用对扩展应用程序调试的方法。因此如果在程序内部打上断点，在程序执行过程中程序将不会在遇到断点的时候停止，然而大部分情况下调试程序仍然是开发过程中不可或缺的环节。好在Xcode只是默认没有启动对扩展应用程序调试的功能，在实际每次调试过程中启用该扩展即可。

## 为App Extension程序开启调试功能
在执行程序之前，确保Xcode运行目标(**target**)选择了`App Extension`程序而非捆绑的软件:
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/DebugforAppExtensions/1.png)

`command + R`运行该扩展程序，选择对应的运行环境:
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/DebugforAppExtensions/2.png)

在调试之前，在Xcode的菜单栏中，选择`Debug` -> `Attach to Process`，找到对应的扩展程序，选中即可进入调试模式:
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/DebugforAppExtensions/3.png)

在任意位置打上断点便可调试程序。

