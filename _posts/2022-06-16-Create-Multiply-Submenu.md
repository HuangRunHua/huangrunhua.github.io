---
layout:     post                       # 使用的布局（不需要改）
title:      Swift 创建多个 NSMenu 子菜单栏
subtitle:   嵌套式菜单栏
date:       2022-8-30               # 时间
author:     Joker Hook                         # 作者
header-img: img/1.jpg     #这篇文章标题背景图片
catalog: true                         # 是否归档
tags:                                #标签
    - swift
    - nsmenu
    - nsmenuitem
    - submenu
---

## 创建单个子菜单栏
```swift
    func createSingleSubMenu() -> (subMenu: NSMenu, menuItem: NSMenuItem) {
        let submenu = NSMenu()
        let mainDropdown = NSMenuItem(title: "1-2", action: nil, keyEquivalent: "")
        submenu.addItem(NSMenuItem(title: "1-2-1", action: nil, keyEquivalent: ""))
        submenu.addItem(NSMenuItem(title: "1-2-2", action: nil, keyEquivalent: ""))
        
        return (submenu, mainDropdown)
    }
```

## 创建包含多个子菜单栏的父菜单栏
```swift
    func createMultiplySubMenu() -> NSMenu {
        let main = NSMenu()
        for _ in 0...6 {
            let singleSubMenu = createSingleSubMenu()
            main.addItem(singleSubMenu.menuItem)
            main.setSubmenu(singleSubMenu.subMenu, for: singleSubMenu.menuItem)
        }
        return main
    }
```

## 创建最上层的父菜单栏
```swift
    func menu() -> NSMenu {
        let main = NSMenu()
        let submenu = createMultiplySubMenu()
        let mainDropdown = NSMenuItem(title: "1", action: nil, keyEquivalent: "")
        main.addItem(mainDropdown)
        main.setSubmenu(submenu, for: mainDropdown)
        return main
    }
```
