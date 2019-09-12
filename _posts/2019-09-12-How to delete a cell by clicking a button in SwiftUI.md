---
layout:     post   				    # 使用的布局（不需要改）
title:      How to delete a cell by clicking a button in SwiftUI				# 标题 
subtitle:   Learn the basic of SwiftUI #副标题
date:       2019-09-12 				# 时间
author:     Joker Hook 						# 作者
header-img: img/6.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - iOS
    - Swift
    - Cocoa Touch
    - Framework
    - Xcode
    - SwiftUI
---

Apple has been launching SwiftUI for a while, and I’m also learning about SwiftUI and benefiting a lot.

Some time ago, I have been learning about list. I learned about the details of how SwiftUI handles List by learning the video of WWDC19. But it is a pity that [WWDC videos](https://developer.apple.com/videos/play/wwdc2019/204/) only provide a way to slide the delete cell, but what I want to achieve is to delete the cell by clicking a button on the cell.

Obviously, the method of sliding to delete a cell is suitable for the table views that their cell’s height is not too tall, like the Mail app and Message app.

![](https://cdn-images-1.medium.com/max/13328/1*vjXHy_m6Oiv5m6AKl3ODsQ.jpeg)

However, if the application you are designing is similar to Twitter or Instagram, the sliding delete method is not appropriate. Obviously, if you use the sliding delete operation in a highly high cell, then the human-computer interaction is too bad.

![](https://cdn-images-1.medium.com/max/13328/1*Ir2S8xwltKn2R5BVrKXhOg.jpeg)

We can see that both Twitter and Instagram provide a button to choose from in the upper right corner. When you press the button, a pop-up window will pop up. In this interface, you can perform a series of operations. This method completely replaces the traditional sliding delete operation, making human-computer interaction more friendly.



### Demo Time

![](https://cdn-images-1.medium.com/max/2000/1*mf9xD0X1sh4rMMsjsK_yJw.gif)

This is the function we will implement next. As you can see, when we click the delete button, the corresponding cell will be deleted.

Maybe you will say that this is very simple. In Swift, you only need to get the indexPath corresponding to the cell.

But since Apple has released SwiftUI, we can use SwiftUI to achieve it.
>  **Create a form interface**

First, you need to create a new single view application.

And then create a Swift file, I named it ***Data.swift***, then add a struct in the file.

```swift
import SwiftUI

struct Music: Identifiable, Hashable {
    var id = UUID()
    var name: String
}
```

We will use the id attribute later. It’s very important, every cell we create needs an id.

Open your ***ContentView.swift***, and add the code in the view(above the body view).

```swift
@State var musicLists = [
    Music(name:"THE GIFT"),Music(name:"Something Just Like This"),
    Music(name:"Dream It Possible"),
    Music(name:"Eyes On Me"),
    Music(name:"Windy Hill"),
    Music(name:"LOVE Theme from TIGA"),
    Music(name:"Victory"),
    Music(name:"Bamboo"),
    Music(name:"Summer")
]
```

Here we have created a data source, which we will use later in the list.

Then, in the body view, add our navigation view and list view:

```swift
NavigationView {
    List {
        ForEach(musicLists) { music in
            HStack {
                Text(music.name)
                Spacer()
                Button(action: {}){
                    Text("Delete")
                }.foregroundColor(Color(.red))
            }
        }
    }.navigationBarTitle("Songs")
}
```

If you have doubts about this code, I highly recommend that you take a look at the [WWDC video](https://developer.apple.com/videos/play/wwdc2019/204/) or [Apple’s SwiftUI tutorial](https://developer.apple.com/cn/xcode/swiftui/), you will understand, because it is very simple.

After adding the above code, you will see in the Canvas that the list has been created.

![](https://cdn-images-1.medium.com/max/3676/1*BprvpUHNqp7JlmiwXAnK_w.png)
>  **Implement the method of deleting the cell**

Now we have to implement the delete function, which is also very simple.

In fact, the principle of deleting a cell is roughly to find the index of the cell corresponding to the click button, and then delete the element of the corresponding position in the data source.

Declare a method outside the body view:

```swift
func delete(at index: Int) {
    musicLists.remove(at: index)
}
```

Yes, you are not mistaken, this method is as simple as that, no need to add other content.

Add this method to the action of the button.

```swift
Button(action: { self.delete(at:)}) {
    ···
}
```

The question we are facing now is what should be written after the ‘at’. Remember our id attribute? The advantage of the id attribute at this time is reflected.

Use the method of [firstIndex](https://developer.apple.com/documentation/coreaudio/unsafemutableaudiobufferlistpointer/2990408-firstindex), it’s powerful.

```swift
Button(action: { self.delete(at:self.musicLists.firstIndex(where: { $0.id == music.id })!)}) {
    ···
}
```

We’re done, we can now run the project in Canvas, and you’ll see that everything is perfect.

Thanks to SwiftUI, we are able to implement complex functions with less code.