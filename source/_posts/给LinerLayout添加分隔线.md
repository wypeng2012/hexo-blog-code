---
title: 给LinerLayout添加分隔线
date: 2018-07-09 19:43:03
tags:
  - android
  - android界面开发
categories: android界面开发
---

# 简介： #
在android开发中，经常会碰到添加分隔线的界面设计，有些同学可能就是直接设置一个view，然后设置宽高和背景色，这也不失为一种方式，但是还有更简单方式，那就是利用LinerLayout自带的divider属性
<!--more-->

# 步骤： #
假设布局如下图：
![设计图](/imgs/linearlayout.png)

1. 在res/drawable/目录下创建一个shape的xml文件，里面代码如下：
	```java
	
	<?xml version="1.0" encoding="utf-8"?>
	<shape xmlns:android="http://schemas.android.com/apk/res/android">
	    <solid android:color="#262936"/>
	    <size android:width="@dimen/dimen_1dp" android:height="50dp"/>
	</shape>

    ```
2. 在你的layout布局中的LinearLayout，添加如下属性：
	```base
	
    android:divider="@drawable/divider_line"
    android:dividerPadding="@dimen/dimen_20dp"
    android:showDividers="middle"

    ```
3. dividerPadding的作用是设置分隔线的内边距
4. showDividers属性，是设置分隔线的显示位置，有四个flag，分别是：begining（开始位置），end（结束位置），middle（中间，最常见的），none（不显示，也是默认值）