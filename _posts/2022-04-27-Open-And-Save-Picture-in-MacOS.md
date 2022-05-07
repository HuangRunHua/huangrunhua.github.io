---
layout:     post   				    # 使用的布局（不需要改）
title:      使用SwiftUI获取macOS任意文件夹下的图片
subtitle:   NSOpenPanel的用法 #副标题
date:       2022-4-27 				# 时间
author:     Joker Hook 						# 作者
header-img: img/6.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - SwiftUI
---

`NSOpenPanel`框架允许开发人员获取macOS系统本地的资源库，如果用户想通过访问路径获取加载本地资源库中的一张照片，可使用以下函数打开仪表盘：
```swift
func showOpenPanel() -> URL? {
    let openPanel = NSOpenPanel()
        openPanel.allowedContentTypes = [.image]
        openPanel.allowsMultipleSelection = false
        openPanel.canChooseDirectories = false
        openPanel.canChooseFiles = true
        let response = openPanel.runModal()
        return response == .OK ? openPanel.url : nil
    }
```

> ### 标注
>
> 请注意，路径有可能并不存在，要注意返回一个可选的路径值，当路径不存在时返回`nil`。

使用如下语句加载用户选择的图片的路径，注意检查路径的存在性:
```swift
if let openURL = showOpenPanel() {
    imageURL = openURL
}
```

在获取到图片路径后便可将图片加载到视图内，使用如下语句加载本地图片：
```swift
Image(nsImage: NSImage(byReferencing: imageURL))
```

