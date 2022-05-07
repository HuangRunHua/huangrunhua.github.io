---
layout:     post                       # 使用的布局（不需要改）
title:      如何将SwiftUI视图转换为图像
subtitle:   drawHierarchy的用法
date:       2022-4-27                 # 时间
author:     Joker Hook                         # 作者
header-img: img/5.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - Swift
    - SwiftUI
---

SwiftUI的视图没有将视图渲染为图像的内置功能，但我们可以在很短的时间内自己编写视图。这里的关键是使用UIHostingController包装视图，然后将其视图层次结构渲染为`UIGraphicsImageRenderer`。

这最好使用`View`上的扩展程序完成，因此您可以自然调用它。这应该将视图包装在托管控制器中，将托管控制器视图的大小调整为SwiftUI视图的内在内容大小，清除任何背景颜色以保持渲染图像干净，然后将视图渲染为图像并将其发送回。

以下是代码中的样子：
```swift
extension View {
    func snapshot() -> UIImage {
        let controller = UIHostingController(rootView: self)
        let view = controller.view

        let targetSize = controller.view.intrinsicContentSize
        view?.bounds = CGRect(origin: .zero, size: targetSize)
        view?.backgroundColor = .clear

        let renderer = UIGraphicsImageRenderer(size: targetSize)

        return renderer.image { _ in
            view?.drawHierarchy(in: controller.view.bounds, afterScreenUpdates: true)
        }
    }
}
```

要在SwiftUI中使用该扩展，您应该将视图创建为属性，以便您可以按需引用它——例如，响应按钮操作。

例如，这会将样式文本视图呈现为图像，然后将其保存到用户的相册中：
```swift
struct ContentView: View {
    var textView: some View {
        Text("Hello, SwiftUI")
            .padding()
            .background(Color.blue)
            .foregroundColor(.white)
            .clipShape(Capsule())
    }

    var body: some View {
        VStack {
            textView

            Button("Save to image") {
                let image = textView.snapshot()

                UIImageWriteToSavedPhotosAlbum(image, nil, nil, nil)
            }
        }
    }
}
```

> **注意**
>
> 要调用`UIImageWriteToSavedPhotosAlbum()`，您必须将`NSPhotoLibraryAddUsageDescription`键添加到`Info.plist`中，并向用户解释为什么要写图像。如果您未能做到这一点，当您尝试写出图像时，您的应用程序将崩溃。
