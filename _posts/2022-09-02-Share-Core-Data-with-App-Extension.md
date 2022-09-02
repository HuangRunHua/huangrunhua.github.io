---
layout:     post                       # 使用的布局（不需要改）
title:      与App扩展共享Core Data
subtitle:   App Group的使用
date:       2022-9-2               # 时间
author:     Joker Hook                         # 作者
header-img: img/2.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - core data
    - app extension
    - app group
---

如果使用 `Core Data` 作为数据库解决方案，则与 Today 扩展、Action 扩展或 Share 扩展共享您的 Core Data 数据库非常常见。在`Filer`应用程序中，主要需要将 Core Data 数据与 Finder 扩展共享。

应用程序扩展和包含的应用程序无法直接访问彼此的容器，即使应用程序扩展包嵌套在其包含的应用程序的捆绑包中。幸运的是，苹果可以轻松地与您的扩展共享持久容器。

## 设置持久容器进行数据共享

要共享数据，我们需要在主应用程序及其扩展之间创建一个共享容器。我们可以通过在 Xcode 的 `Signing & Capabilities` 里选择给软件添加 `App Group`。

App Group 添加后便可以让核心App与扩展程序共享App Group下存储的核心数据了，具体实施在于需要将Core Data的存储路径更改为特定的 App Group 下，在`PersistenceController.swift`文件内手动设置存储路径：

```swift
struct PersistenceController {
    static let shared = PersistenceController()

    static var preview: PersistenceController = {
        // Preview preparation
        ...
    }()

    let container: NSPersistentContainer

    init(inMemory: Bool = false) {
        container = NSPersistentContainer(name: "FileTemplatesModel")
        if inMemory {
            // If inMemory ...
        }
        /// The following code saves the database to the shared App Container
        let storeURL = URL.storeURL(for: "group.com.noreply-email.Filer", databaseName: "FileTemplates")
        let storDescription = NSPersistentStoreDescription(url: storeURL)
        container.persistentStoreDescriptions = [storDescription]
        
        container.viewContext.automaticallyMergesChangesFromParent = true
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
    }
}
```

为实现上述功能，需要给URL设置一个扩展来使用安全应用程序组标识符获取共享容器的路径：
```swift
public extension URL {
    /// Returns a URL for the given app group and database pointing to the sqlite database.
    static func storeURL(for appGroup: String, databaseName: String) -> URL {
        guard let fileContainer = FileManager.default.containerURL(forSecurityApplicationGroupIdentifier: appGroup) else {
            fatalError("Shared file container could not be created.")
        }

        return fileContainer.appendingPathComponent("\(databaseName).sqlite")
    }
}
```

完成上述工作后，Core Data已经准备好与核心程序及其扩展程序共享了。

## 在扩展程序内使用Core Data

在扩展程序内添加如下语句来初始化`PersistenceController`:
```swift
private let persistenceController = PersistenceController.shared
```

为了获取所有数据，我们可以将获取功能包装成一个方法：
```swift
private func fetchTemplatesFromCoreData() -> [YOURCOREDATAMODEL] {
    let request = YOURCOREDATAMODEL.fetchRequest()
    var results: [YOURCOREDATAMODEL] = []
    self.persistenceController.container.viewContext.performAndWait {
        do {
            results = try self.persistenceController.container.viewContext.fetch(request)
        } catch {
            results = []
        }
    }
    return results
}
```

在程序任意处调用上述方法即可获取所有数据。
