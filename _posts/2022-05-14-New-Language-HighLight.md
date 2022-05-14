---
layout:     post                       # 使用的布局（不需要改）
title:      xclangspec文件分析
subtitle:   使Xcode支持自定义语法高亮
date:       2022-5-14                 # 时间
author:     Joker Hook                         # 作者
header-img: img/3.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - Xcode
---

Xcode的程序包路径下存在以`.xclangspec`为后缀的文件夹。这些文件夹定义了Xcode用到的各种语言的语法说明，为`JSON`格式的文件。

> 在Xcode程序包中进入如下文件夹即可找到`.xclangspec`文件
> /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/


同时在相同路径下存在`DVTFoundation.xcplugindata`文件夹，配置该文件夹才可使的Xcode懂得支持的语言集合。

完成上述步骤后在终端执行如下代码:
```sh
rm -f /private/var/folders/*/*/*/com.apple.DeveloperTools/*/Xcode/PlugInCache-Debug.xcplugincache
```
即可支持自定义代码文件。
