---
title: Android-Activity & Fragment
date: 2016-09-25 19:43:56
tags:
    - Android
---

<!-- more -->

## Activity的回调机制-----命令者模式

对于一个具有通用性质的程序架构来说，程序架构完成整个应用的通用功能、流程，但在某个特定的点上，需要一段业务相关的代码---但是通用架构无法实现这段代码，于是程序架构在这个点上留一个“空”。对Java程序，实现形式：一是以接口方式存在，二是以抽象方法存在。

***
## Activity的生命周期
* 运行状态：位于前台，用户可见，获得焦点
* 暂停状态：不在前台，依然可见，失去焦点
* 停止状态：不可见，失去焦点
* 销毁状态：结束

![activity]({{ site.url }}/assets/imgs/android/activity.jpg)

***
## activity和servlet的异同

>同：
* 都是像用户呈现界面
* 开发者开发activity和servlet都需要继承系统的基类
* 两者开发出来后都需要配置
* 开发者无需创建两者的实例，无需调用两者的方法，两者的方法都由系统以回调方式来调用
* 都有各自的生命周期，两者的生命周期由外部负责管理
* 两者各自之间都不会相互调用，也不能直接进行数据交流，servlet需要借助requestScope、sessionScope等，activity间需借助Bundle

>异：
* Activity运行在Android应用中，servlet运行在web应用中
* Activity是Android窗口的容器，所以Activity最终是以窗口形式展现的；而servlet不会生成界面，只是向浏览器生成响应，界面是由浏览器负责生成
* Activity间的跳转主要由Intent对象来控制，而servlet间主要是由用户请求来控制

***

## Activity的4种加载方式
1. standard模式：默认模式。为目标Activity创建新的实例，并将该Activ添加到当前Task栈中
2. singleTop模式：与standard模式基本相同，不同在于，当将要启动的Activity已经存在Task中且位于栈顶的时候，系统不会创建新的Activity
3. singleTask模式：若目标Activity不存在，则创建之并加入Task栈顶；若目标Activity已经位于Task栈顶，与singleTop模式相同；若目标Activity存在但不在栈顶，系统将该Activity上的所有Task移出栈，使得该Activity成为栈顶。
4. singleInstance模式：若目标Activity不存在，系统先和创建全新的Task，再创建目标Activity实例，并加入新Task中；若目标Activity存在，无论其在哪里，系统都将其所在的Task转为前台，并使其显示出（该Task中只有一个Activity？）

***

## fragment的生命周期
* 运行状态：位于前台，用户可见，获得焦点
* 暂停状态：不在前台，可见，但不能获得焦点
* 停止状态：不可见，失去焦点

![fragment]({{ site.url }}/assets/imgs/android/fragment.jpg)


*** 


