---
layout:     post   				    # 使用的布局（不需要改）
title:      使用SwiftUI的AsyncImage从URL渲染远程图像 		
subtitle:   异步加载图片的新方式 #副标题
date:       2022-5-7 				# 时间
author:     Joker Hook 						# 作者
header-img: img/2.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - Swift
    - iOS
    - ImageLoader
    - SwiftUI
---

> ### 标注
>
> 本文翻译自[Using SwiftUI’s AsyncImage to render remote images from URLs](https://wwdcbysundell.com/2021/using-swiftui-async-image/)

2021 年发布的SwiftUI推出了一种名为`AsyncImage`的全新内置视图，它提供了一种从`URL`下载和渲染远程图像的简单方法。在非常基本的层面上，如果我们只想按原样呈现下载的图像，那么现在可以这样完成：
```swift
struct ProductView: View {
    var product: Product

    var body: some View {
        VStack {
            AsyncImage(url: product.imageURL)
            ...
        }
    }
}
```

默认情况下，`AsyncImage`将根据其像素大小渲染下载的图像，而不会以任何方式裁剪或调整其大小。在某些情况下，这可能是我们想要的，但在某些情况下，这可能会导致我们的图像被绘制得太大——甚至可能超出屏幕范围。

关于如何解决这个问题的初步想法，例如通过将我们的图像限制在一定的最大宽度和高度，可能是使用SwiftUI中常用的帧修饰符：
```swift
struct ProductView: View {
    var product: Product

    var body: some View {
        VStack {
            AsyncImage(url: product.imageURL)
                .frame(maxWidth: 300, maxHeight: 100)
            ...
        }
    }
}
```

然而，上述内容只会更改用于渲染我们图像的容器的帧，它实际上并没有调整我们图像本身的大小（最终仍然可能被绘制出界）。为了实际更改图像的大小，我们必须使用另一个`AsyncImage`初始化器，该初始化器使我们能够更精确地控制图像及其占位符的渲染方式：
```swift
struct ProductView: View {
    var product: Product

    var body: some View {
        VStack {
            AsyncImage(
                url: product.imageURL,
                content: { image in
                    image.resizable()
                         .aspectRatio(contentMode: .fit)
                         .frame(maxWidth: 300, maxHeight: 100)
                },
                placeholder: {
                    ProgressView()
                }
            )
            ...
        }
    }
}
```

真正整洁的是，由于上述`content`闭包为我们提供了下载的Image，并使我们能够返回任何我们希望用于表示它的视图，我们现在可以完全控制每张图像的渲染方式。

然而，需要记住的一件事是，从`placeholder`闭包返回的视图不仅在加载图像时使用，还用作回退，以防遇到某种形式的错误。同样，对于某些用例来说，这可能没问题，但在上面，我们希望在加载图像时显示`ProgressView`，但如果下载失败，我们可能不想继续显示加载旋转器。

有办法解决这个问题，使用`AsyncImage`目前附带的第三种类型的初始化器——这使我们能够实现一个闭包，根据下载操作当前所在的`AsyncImagePhase`构建视图的内容。以下是我们如何使用该API来决定是渲染图像本身、加载旋转器还是占位符，以防下载失败：
```swift
struct ProductView: View {
    var product: Product

    var body: some View {
        VStack {
            AsyncImage(url: product.imageURL) { phase in
                switch phase {
                case .empty:
                    ProgressView()
                case .success(let image):
                    image.resizable()
                         .aspectRatio(contentMode: .fit)
                         .frame(maxWidth: 300, maxHeight: 100)
                case .failure:
                    Image(systemName: "photo")
                @unknown default:
                    // Since the AsyncImagePhase enum isn't frozen,
                    // we need to add this currently unused fallback
                    // to handle any new cases that might be added
                    // in the future:
                    EmptyView()
                }
            }
            ...
        }
    }
}
```

我们还可以传递一些其他参数，包括我们期望下载图像的规模。然而，似乎不可能的一件事是，我们控制实际的下载操作本身。`AsyncImage`的文档指出，它将始终使用应用程序的默认`URLSession.shared`实例来执行每次网络调用，除了`URLSession`本身内置的缓存外，似乎没有办法在上面添加任何额外的缓存层。
