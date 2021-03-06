---
layout:     post   				    # 使用的布局（不需要改）
title:      Load local images asynchronously in the table view 				# 标题 
subtitle:   Learn something about NSCache #副标题
date:       2019-08-30 				# 时间
author:     Joker Hook 						# 作者
header-img: img/1.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - iOS
    - Swift
---

Chinese version -> [Here](https://medium.com/cocoa-touch-things/%E5%9C%A8%E8%A1%A8%E6%A0%BC%E8%A7%86%E5%9B%BE%E9%87%8C%E9%9D%A2%E5%BC%82%E6%AD%A5%E5%8A%A0%E8%BD%BD%E6%9C%AC%E5%9C%B0%E5%9B%BE%E7%89%87-9894799090d8)

Table views have always been the most popular guests for many apps. In fact, more than 90% of the software has a tabular view. Numerous UI designers like to add images to the table view, such as the most popular Twitter and Instagram.

It’s good to use the image in the table view, but it will bring a problem. The image loading time is longer than other content. If the image loading process is placed in the main thread, it will cause the table view to appear unsmooth when sliding.



### Table of Contents

* Load image project

* Add picture control

* Create a data source

* Improve the table view

* Select image function

* Problem found

* Solve the problem that the table view does not slide smoothly

  

### Load image project

Let’s try to load the image in the main thread.

Open your Xcode, create a new project, select the single view app option, and name your project LazyLoadImage.

Then open your storyboard (Main.storyboard) and delete the pre-created view controller. Then drag a table view controller from the Library to the story panel.

Add a navigation bar to the table view controller (specifically select table view controller -> editor -> embed in -> navigation controller)

Add a button UIBarButtonItem to the navigation bar, then add a title to the navigation bar, which I added here as Images.

![](https://cdn-images-1.medium.com/max/2000/1*2os68piFUIsPXKHfkBRMNA.png)

In order to more intuitively see the difference between asynchronous loading and synchronous loading of images, I will use a relatively simple interface to describe here.
Select the cell, open Attribute Inspector, and set the cell’s identifier. And open the Size Inspector and set the height of the cell to 300.

![](https://cdn-images-1.medium.com/max/2000/1*wdDoHgGCu8aX3huvgdzkeQ.png)

![](https://cdn-images-1.medium.com/max/2000/1*dkkGedtXiZkK1dE92h4Hsg.png)





### Add picture control

Drag an ImageView from the Library onto the cell and set the four sides of the image view to fit inside the cell.

![](https://cdn-images-1.medium.com/max/5760/1*3OBL7Oyd5J27z0OmcGQdKg.png)

Note that the content mode of the image view is set to Aspect Fill in the Attribute Inspector.





### Create a data source

Create a new Swift file and name it Data.swift.
Since we only have one element, UIImageView, there is only one member in our class.

```swift
import UIKitclass Image: Codable {
    var imageData: Data?
    
    init(imageData: Data) {
        self.imageData = imageData
    }
    
    static let DocumentsDirectory = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
    static let ArchiveURL = DocumentsDirectory.appendingPathComponent("Image").appendingPathExtension("plist")
    
    static func loadImages() -> [Image]? {
        guard let codedImages = try? Data(contentsOf: ArchiveURL) else { return nil }
        
        let propertyListDecoder = PropertyListDecoder()
        return try? propertyListDecoder.decode(Array<Image>.self, from: codedImages)
    }
    
    static func loadSampleImages() -> [Image] {
        return []
    }
    
    static func saveImages(_ images: [Image]) {
        let propertyListEncoder = PropertyListEncoder()
        let codedImages = try? propertyListEncoder.encode(images)
        try? codedImages?.write(to: ArchiveURL, options: .noFileProtection)
    }
}
```



Knowledge about [Codable](https://developer.apple.com/documentation/swift/codable) and [Decodable](https://developer.apple.com/documentation/swift/decodable) can be found in [App Development with Swift](https://books.apple.com/book/id1465002990).

Then create the actual data:

```swift
var images: [Image] = []
```





### Improve the table view

Create two Swift files, one for [TableViewController.swift](https://github.com/HuangRunHua/LazyLoadImage/blob/master/LazyLoadImage/TableViewController.swift/)and one for [TableViewCell.swift](https://github.com/HuangRunHua/LazyLoadImage/blob/master/LazyLoadImage/TableViewCell.swift).

Drag the image view to the created TableViewCell.swift and name it lazyImageView.

```swift
@IBOutlet weak var lazyImageView: UIImageView!
```

Go back to the UITableViewController.swift file and add the following code to the viewDidLoad method.

```swift
override func viewDidLoad() {
        super.viewDidLoad()
        tableView.estimatedRowHeight = 300
        tableView.rowHeight = UITableView.automaticDimension
        
        if let savedImages = Image.loadImages() {
            images = savedImages
        } else {
            images = Image.loadSampleImages()
        }
}
```

In order to be able to display the tabular data, we also need to add a proxy method for the table view.

Implement the table view proxy in the [***numberOfSections(in tableView: )***](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614860-numberofsections)，[***numberOfRowsInSection***](https://developer.apple.com/documentation/uikit/uitableview/1614952-numberofrowsinsection?language=occ)，[***cellForRow(at:)***](https://developer.apple.com/documentation/uikit/uitableview/1614983-cellforrow) and [***tableView(_:heightForRowAt:)***](https://developer.apple.com/documentation/uikit/uitableviewdelegate/1614998-tableview) :

```swift
override func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return images.count
    }override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath) as! TableViewCellcell.lazyImageView.image = UIImage(data:images[indexPath.row].imageData!)
        return cell
    }override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return 300
    }
```





### Select image function

Select the button above the navigation bar, hold down the control key, drag a line into the TableViewController.swift file, select the `IBAction` event, and name the event addNewImageButtontapped:

```swift
@IBAction func addNewImageButtontapped(_ sender: UIBarButtonItem) {
}
```

We will select the images from the album and add them to the images, which will be displayed on the table view.

Add the following statement to your `addNewImageButtonTapped(sender:)` method:

```swift
let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        
        let alertViewController = UIAlertController(title: "Choose Image Source", message: nil, preferredStyle: .actionSheet)
        let cancelAction = UIAlertAction(title: "Cancel", style: .cancel, handler: nil)
        
        if UIImagePickerController.isSourceTypeAvailable(.photoLibrary) {
            let photoLibraryAction = UIAlertAction(title: "Photo Library", style: .default, handler: { action in
                imagePicker.sourceType = .photoLibrary
                self.present(imagePicker, animated: true, completion: nil)
            })
            alertViewController.addAction(photoLibraryAction)
        }alertViewController.addAction(cancelAction)
        present(alertViewController, animated: true, completion: nil)
        
        // --- 没有这一句话会有约束警告 ---
        alertViewController.view.subviews.flatMap({$0.constraints}).filter{ (one: NSLayoutConstraint)-> (Bool)  in
            return (one.constant < 0) && (one.secondItem == nil) &&  (one.firstAttribute == .width)
            }.first?.isActive = false
```

In order to be able to select images, we also need to add a proxy for image selection, adding two superclasses to the TableViewController: UIImagePickerController and UINavigationControllerDelegate.

```swift
class TableViewController: UITableViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate {
·······
}
```

Then implement the [***imagePickerController(_:didFinishPickingMediaWithInfo:)***](https://developer.apple.com/documentation/uikit/uiimagepickercontrollerdelegate/1619126-imagepickercontroller) method in UITableViewController.swift:

```swift
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        if let selectedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage {
            
            let image = Image(imageData: selectedImage.pngData()!)
            images.append(image)
            Image.saveImages(images)
            dismiss(animated: true, completion: nil)
    }
```

Now, if you run the project directly and click the Add Image button, the project will crash, because you have not added the relevant permissions in Info.pilst. Open this file and add a new project to it: Privacy — Photo Library Usage Description.

![](https://cdn-images-1.medium.com/max/3636/1*wKtZLwdFASZItmNBXKYe-Q.png)

Restart your project, try to add images to it, try to add larger quality images, the effect will be more obvious.





### Problem found

It’s not hard to find that when you slide the table view, you will find that the table view is not smooth. This is due to the high quality of the image. Because the image quality is relatively large, you can slide the table view faster. The image has not been loaded yet, which leads to a phenomenon similar to the system, which is why the table view is not smooth.

![](https://cdn-images-1.medium.com/max/2000/1*lWgZ1ku0vuSi9_EYbIFdhA.gif)





### Solve the problem that the table view does not slide smoothly

The problem has been thrown out and we are going to start solving this problem. There are actually many solutions. The simplest method is to compress the quality of the image. As long as the quality of the image is small, it will not cause this kind of stuck problem.

In fact, many developers don’t use this approach because it’s not good, and most developers use this method as one of the steps to solve the problem.

In fact, most developers prefer the way that loads images asynchronously.
>  Asynchronous Loading Knowledge

Asynchronous loading is loaded at the same time as the execution process, usually the things that are of less importance to the images.

Let’s take a look at how to use the asynchronous loading method in the project.
>  Asynchronous Loading Of Images

This feature can be easily implemented using [***DispatchQueue***](https://developer.apple.com/documentation/dispatch/dispatchqueue).
Rewrite in your [***cellForRow(at:)***](https://developer.apple.com/documentation/uikit/uitableview/1614983-cellforrow) method:

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
 		let cell = tableView.dequeueReusableCell(withIdentifier: “Cell”, for: indexPath) as! TableViewCell
 		cell.lazyImageView.image = nil
 		DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + 0.75) {
 				//获取cell真正的indexPath.
 				if let index = tableView.indexPath(for: cell) {
 								if let data = images[index.row].imageData {
 								cell.lazyImageView.image = UIImage(data: data)
 								cell.setNeedsLayout()
 								}
 				}
 		}
 return cell
}
```

Run the project, this time you should be able to see a lot of smooth images, and the images are loaded afterward.

![](https://cdn-images-1.medium.com/max/2000/1*5ERzjPMEHRZdV4I5bpmCFQ.gif)
>  New problem

There is a small problem with the project. You will see that the images outside the screen will be loaded repeatedly. We want to load those images when they are outside the screen, but they are already loaded when they return to the screen. The actual state of the picture appears instead of after the interface is loaded for a while.

This involves the knowledge of caching.

Here, in order to achieve this function, we need to use [***NSCache***](https://developer.apple.com/documentation/foundation/nscache).

Add a variable inside the Data.swift file:

```swift
let imageCache = NSCache<AnyObject, AnyObject>()
```

At the same time, in order to be able to cache images, we need to use a String type keyword to retrieve the image. Add a variable to your Image class:

```swift
var recordID: String?
init(imageData: Data, recordID: String) {
    self.imageData = imageData
    self.recordID = recordID
}
```

recordID is a unique search keyword for each image. Here, you can use `*String(data: selectedImage.pngData()!, encoding: .unicode)!*`

Modify the [***imagePickerController(_:didFinishPickingMediaWithInfo:)***](https://developer.apple.com/documentation/uikit/uiimagepickercontrollerdelegate/1619126-imagepickercontroller) method:

```swift
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        if let selectedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage {
            
            // 获取图片唯一的ID，这里使用String(data: selectedImage.pngData()!, encoding: .unicode)!
            let image = Image(imageData: selectedImage.pngData()!, recordID: String(data: selectedImage.pngData()!, encoding: .unicode)!)
            images.append(image)
            Image.saveImages(images)
            dismiss(animated: true, completion: nil)
        }
    }
```

Also need to modify the cellForRow(at:) method:

```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath) as! TableViewCell/**
         *另外在下载图片之前先把cell的imageView的image置为nil
         *可以防止图片下载失败而导致显示了以前的图片.
         *如果照片有在缓存里面就去缓存里面取，没有就添加到缓存里面
         */
        cell.lazyImageView.image = nilif let imageFromCache = imageCache.object(forKey: images[indexPath.row].recordID as AnyObject) as? UIImage {
            cell.lazyImageView.image = imageFromCache
        } else {
            DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + 0.75) {
                // 获取cell真正的indexPath.
                if let index = tableView.indexPath(for: cell) {
                    if let data = images[index.row].imageData {
                        let imageToCache = UIImage(data: data)
                        // 给缓存添加照片
                        imageCache.setObject(imageToCache!, forKey: images[indexPath.row].recordID! as AnyObject)
                        cell.lazyImageView.image = UIImage(data: data)
                        cell.setNeedsLayout()
                    }
                }
            }
        }
        return cell
    }
```

Refer to the comments for specific implementation features of the code.

If you run this project at this time, you will find that the project crashes. This is because the recordID is newly added. The original images have no recordID content, so you need to delete the project on the emulator and re-run it.

The whole project you can find on [GitHub](https://github.com/HuangRunHua/LazyLoadImage).