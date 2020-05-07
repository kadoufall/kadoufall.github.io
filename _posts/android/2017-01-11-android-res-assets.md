---
title: Android-资源res & assets
date: 2017-01-11 12:16:56
tags:
    - Android
---

<!-- more -->

## Android将资源放置在res和assets两个目录下
* res：各种字符串、颜色、数组、菜单资源，Android SDK在编译时自动在R.java文件中为这些资 源创建索引，程序可以直接通过R资源清单类进行访问
* assets：存放应用无法直接访问的原生资源，需要通过AssetManager以二进制形式读取资源

 