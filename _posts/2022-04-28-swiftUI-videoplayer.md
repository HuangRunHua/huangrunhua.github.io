---
layout:     post   				    # 使用的布局（不需要改）
title:      在SwiftUI中使用VideoPlayer播放视频				# 标题 
subtitle:   iOS14的新特性 #副标题
date:       2022-4-28 				# 时间
author:     Joker Hook 						# 作者
header-img: img/7.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - SwiftUI
---

SwiftUI的`VideoPlayer`视图允许我们从任何本地或远程URL播放电影。它来自`AVKit`框架，因此在试用之前，您应该确保并添加导`入AVKit`。
```swift
import AVKit
```

例如，如果您的应用程序包中有`video.mp4`，并想播放它，您将使用此功能：
```swift
VideoPlayer(player: AVPlayer(url:  Bundle.main.url(forResource: "video", withExtension: "mp4")!))
    .frame(height: 400)
```

如果您想播放远程视频，请使用其远程URL：
```swift
VideoPlayer(player: AVPlayer(url:  URL(string: "https://bit.ly/swswift")!))
    .frame(height: 400)
```

如果愿意，您可以向`VideoPlayer`初始化器提供第二个参数，该参数将要在视频上绘制的内容添加。这将绘制在系统视频控件下方，但可以响应这些控件未捕获的任何事件。

例如，这会将文本“水印”放在视频区域的最顶部：
```swift
VideoPlayer(player: AVPlayer(url:  URL(string: "https://bit.ly/swswift")!)) {
    VStack {
        Text("Watermark")
            .foregroundColor(.black)
            .background(Color.white.opacity(0.7))
        Spacer()
    }
    .frame(width: 400, height: 300)
}
```

