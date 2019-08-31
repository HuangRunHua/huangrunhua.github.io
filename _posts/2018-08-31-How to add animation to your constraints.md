---
layout:     post   				    # 使用的布局（不需要改）
title:      How to add animation to your constraints				# 标题 
subtitle:   Learn something about animation #副标题
date:       2019-08-31 				# 时间
author:     Joker Hook 						# 作者
header-img: img/3.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - iOS
    - Swift
    - Animation
    - Constraints
---

### Introduce

The animation is a very important element in the development of software, and at the same time animation can make the user experience more perfect. Recently I tried to develop my first Cocoa Touch Framework — “[HReminder](https://github.com/HuangRunHua/HReminder)”. it’s similar to the button that Medium uses it to remind readers to “Start from the beginning”.

![](https://cdn-images-1.medium.com/max/12268/1*DsAs67KS9oXGsdNvNnmlog.jpeg)

But the process of frame development is not so smooth. I don’t use the storyboard to set the button’s constraints, instead, I use codes to build them.

![](https://cdn-images-1.medium.com/max/2960/1*KOMOFtKF4I5HRWhHXeXj0g.png)

Then the trouble comes up to me. I need to reset the button’s top constraint, in the beginning, the button will appear out of the screen, then when the user taps the bar button, it will turn up just 16 px below the navigation bar with animation.

However, when I use UIView.animate(), it doesn’t work. (At first, I set the viewOfTopConstrian’s constant to -200.)

```swift
UIView.animate(withDuration: 0.25, animations: {() in
    viewOfTopConstrian.constant = 16
})
```

Then I find the viewOfTopConstrian is an object of NSLayoutConstraint, UIView.animate does not work for it. The only thing I need to do is to use *layoutIfNeeded()*. In order to use it, when I initialized the view, I set the constraint on top of it directly, and I set it to 16px from the bottom of the navigation bar. I didn’t set the view to appear outside the screen at the beginning. Instead, I set the constraints on the button directly. This is also the benefit of using layoutIfNeeded().

```swift
UIView.animate(withDuration: 0.25, animations: {() in
    superview.layoutIfNeeded()
})
```

We can use this method to force the view to update its layout immediately. When using Auto Layout, the layout engine updates the position of views as needed to satisfy changes in constraints. Using the view that receives the message as the root view, this method lays out the view subtree starting at the root. If no layout updates are pending, this method exits without modifying the layout or calling any layout-related callbacks.

Ok, it works🤪.



### Do it step by step

Ok, now let’s do it step by step. In this course, I will teach you how to add animation to your constraints. Here is the project.

![](https://cdn-images-1.medium.com/max/2000/1*sGIfY-fQNb0o_7mOdo-wEA.gif)

This project is very easy when you tap any place of the screen, the width of the red view will change, and with animation.

Ok, let’s begin.

First, create a new single view application, name it anything. then drag a view to your storyboard. And set its background to red, and its width and height to 100.

Also, you need to add some constraints. Set the red view “horizontally in container” and “vertically in contriner”.

![](https://cdn-images-1.medium.com/max/5760/1*MOU68-IS5LUAAHx9cdbCKw.png)

Then open your ViewController.swift file, tap the control key and drag the view to your file, set its name to redView.

```swift
@IBOutlet weak var redView: UIView!
```

Then add this function below viewDidLoad() function:

```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {

}
```

In this function, you are going to write down your codes to change the red view’s width.

So open your document Outline, and find the red view’s width constraint.

![](https://cdn-images-1.medium.com/max/5760/1*V-2KU9r6J3TYec39-bGdug.png)

Similarly, tap control key and drag it to your swift file. Set its name to ‘redViewW’. You will find that it is an object of *NSLayoutConstraint*.

Ok, come back to your *touchesBegan()* function. And those lines in it:

```swift
UIView.animate(withDuration: 2.0, animations: {() in
    self.redViewW.constant = 50
})
```

Run this project to see what’s going on.

It doesn’t work, right? The width of the red view only changed abruptly, and the animation duration was less than 2 seconds. It’s not an animation.

![](https://cdn-images-1.medium.com/max/2000/1*fJnSybXsfDY7IxafgejcBw.gif)

It’s time to use *layoutIfNeeded().*

Add the line just below the self.redViewW.constant = 50.

```swift
UIView.animate(withDuration: 2.0, animations: {() in
    // 强制刷新
    self.redViewW.constant = 50
    self.view.layoutIfNeeded()
})
```

Run your project, you will find it actually works.

Thank you !!!