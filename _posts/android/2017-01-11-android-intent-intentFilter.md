---
title: Android-Intent & IntentFilter
date: 2017-01-11 12:15:56
tags:
    - Android
---

<!-- more -->

## Intent

**Intent**：用于封装程序启动的活动，Activity、Service、BroadcastReceiver的启动和之间的通讯都是通过Intent进行

***

## Intent的Component属性


接受一个ComponentName对象
```java 
ComponentName comp = new ComponentName(MainActivity.this,SecondActivity.class);
Intent intent = new Intent();
intent.setComponent(comp);
 ```
简化：
```java 
Intent intent = new Intent(ComponentAttr.this,SecondActivity.class);
 ```
***

## Action, Category属性
源Activity:
```java 
public final static String ACTION = "kadoufall.intent.action.ACTION";
Intent intent = new Intent();
intent.setAction(MainActivity.ACTION);
startActivity(intent);
```
目标Activity的配置：
 ```xml
<Intent-filter>
    <!-- 指定该Activity能响应Action属性值为指定字符串的Intent -->
    <action android:name="kadoufall.intent.action.ACTION" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

>对于Category

源Activity添加:
```java 
public final static String CATEGORY = "kadoufall.intent.category.CATEGORY";
intent.addCategory(MainActivity.CATEGORY);
```

***
## Data、Type属性
* Data与Type属性会相互覆盖，以后设置的为准
* 若同时设置，可`setDataAndType()`

***

## `<intent-filter../>`元素通常包含以下子元素
* 0--N个`<action../>`子元素
* 0--N个`<category../>`子元素
* 0--1个`<data../>`子元素

 

 

 

 

 

 

 

 