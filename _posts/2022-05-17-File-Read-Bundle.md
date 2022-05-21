---
layout:     post                       # 使用的布局（不需要改）
title:      在Command Line Tools里面读取文件
subtitle:   自定义Bundle文件
date:       2022-5-17                 # 时间
author:     Joker Hook                         # 作者
header-img: img/4.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - Xcode
    - command line tools
---

通常，在Command Line Tools里面Xcode无法直接读取外部添加的文档文件。然而可以通过自定义添加一个Bundle来实现对外部文档的读取。

在Xcode里, 选择**File --> New --> Target**，在Target内选择**Bundle**，自定义该Bundle的名称，如`DataBundle`。

将文件添加到此捆绑包中。将文件添加到Xcode，然后确保捆绑包在其目标成员资格部分中被勾选：
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/CLT/1.png)

最后将捆绑包添加到您的目标中。在目标的构建阶段，打开复制文件阶段，然后单击+按钮。选择捆绑包，然后单击添加：
![](https://github.com/HuangRunHua/huangrunhua.github.io/raw/master/img/CLT/2.png)

完成上述步骤便可以在程序内读取到文档, 假定文档名字为`untitled.txt`, 在`main.swift`内添加如下访问代码:
```swift
let currentDirectoryURL = URL(fileURLWithPath: FileManager.default.currentDirectoryPath)
let bundleURL = URL(fileURLWithPath: "DataBundle.bundle", relativeTo: currentDirectoryURL)

if let bundle = Bundle(url: bundleURL) {
    if let path = bundle.path(forResource: "untitled", ofType: "txt") {
        if let contents = try? String(contentsOfFile: path) {
            print(contents)
        }
    } else {
        print("No file Found")
    }
}
```

