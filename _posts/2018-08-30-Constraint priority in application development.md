---
layout:     post   				    # 使用的布局（不需要改）
title:      Constraint priority in application development 				# 标题 
subtitle:   Learn something about Constraint priority #副标题
date:       2019-08-30 				# 时间
author:     Joker Hook 						# 作者
header-img: https://unsplash.com/@emilep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - iOS
    - Swift
---


I think, not everyone knows that Xcode has a button that sets the priority of the constraint.

To be honest, I also learned the concept of “priority of constraints” recently. At that time I was talking about some functions of constraint with a professor. Inadvertently we talked about the priority of the constraint. I was confused, I had no idea what he was talking about, and he was also surprised that I didn’t know the concept.

When we set constraints on some controls, we can set the priority of these constraints. Just click on these constraints in the ***Attribute Inspector*** and you will see the option to set the constraint priority. The default is 1000, there are some optional values, Apple recommends us to use 250, 750 and 1000, but in fact, you can choose any number from 0 to 1000.

![](https://cdn-images-1.medium.com/max/2000/1*FQm7vuldtXBcSxQob5RQwg.png)

In the [Apple developer documentation](https://developer.apple.com/documentation/uikit/uilayoutpriority), Apple introduced the constraint priority:
> # The layout priority is used to indicate to the constraint-based layout system which constraints are more important, allowing the system to make appropriate tradeoffs when satisfying the constraints of the system as a whole.

Let us understand this concept with a simple example.

### Demo Time

Now, create a new single view application, open Main.storyboard. Drag three views in your storyboard. To make it easier to see the changes in the view, change the background color of your three views. Here, I use red, pink and gray.

Next set the first view’s(red view) constraints：

![](https://cdn-images-1.medium.com/max/2000/1*yQUpjMNdegG5w_aQOTszYw.png)

OK, let’s set the second view’s constraints. Tap ***Control*** key and drag a line from the pink view to red view, choose “Equal widths” and “Equal heights”. Also, choose “Bottom” and “Horizontal Spacing”.

![](https://cdn-images-1.medium.com/max/2000/1*ISQ9RdbPum3PrmNeGUVe2w.png)

In the document outline, change the pink view’s name to “Pink View”, and change the gray view’s name to “Gray View”. choose the “***Horizontal Spacing Constraints***” and set the spacing to 20.

Repeat the work just now and set the same constraints for the gray view. Set it to a distance of 20 from the pink view, the bottom is parallel to the bottom of the pink view, and the height and width are the same as the height and width of the pink view.

![](https://cdn-images-1.medium.com/max/5760/1*gaAwWJx6trFzotcauXpx5w.png)

Next, open your ViewController.swift file, tap control and drag your pink view to the file. Set its name “pinkView”.

    **@IBOutlet** **weak** **var** pinkView: UIView!

Add the following statement just below the ***viewDidLoad()*** method:

    **override** **func** touchesBegan(**_** touches: Set<UITouch>, with event: UIEvent?) {
        **self**.pinkView.removeFromSuperview()
    }

This method is very simple, when you touch any place of the screen, the pink view will be removed from the view.

Ok, now run your app to see what’s going on.

You see, when you touch the screen, the red view disappears, but, where is the gray view?

![](https://cdn-images-1.medium.com/max/2000/1*XFZtv3QBnc4bj4fw8pkbkA.gif)

Fine, it’s very easy to explain it. The gray view constraint is based on the red view, which means that when there is a red view, the gray view constraint exists, but when the red view disappears, the constraint of the gray view itself disappears. And where will it go, we don’t know…

Maybe you will say, you can set the gray view to its own constraints, no need for a pink view. Yes, I agree with you. However, let us imagine a demand. One day, your boss comes in and tells you that before a user taps the screen, the gray view must stay 20px from red view, and when a user taps the screen, the pink view disappears, but the gray view moves to the place where the pink view has stayed. How can you do that?
>  It’s time to use constraint priority

Now, open your Main.storyboard, and choose the gray view.

Set its width and height just the same as the pink view’s. And then set it to a distance of 20 from the red view, the bottom is parallel to the bottom of the red view. Don’t be afraid of the errors that Xcode reports, we will fix them.

![](https://cdn-images-1.medium.com/max/2000/1*V0P6lIL6CSnwT9BRK77avw.png)

Ok, as you can see, just below the ***constant line, ***there is the ***Priority button.***

Now set the value to 750(or any number you want).

![](https://cdn-images-1.medium.com/max/2000/1*lviCQwOONA8yeeqmguMlgw.png)

You will discover that the errors have gone. Run your project to see what happens.

![](https://cdn-images-1.medium.com/max/2000/1*Eemnx60iO8gILFBTMIhfbQ.gif)

As you will see, when you tap the screen, the pink view disappears, but the gray view does not disappear, and it also moves to the location of the original pink view. This is because the level of constraints created based on the pink view is relatively high, and the program prioritizes the creation of high-level constraints when the view is created. When the pink view disappears, the high-level constraint disappears and the program creates a lower-level constraint.

Thank you for reading my article. 😄