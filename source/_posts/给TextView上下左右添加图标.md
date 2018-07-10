---
title: 给TextView上下左右添加图标
date: 2018-07-10 18:03:07
tags:
  - android
  - android界面开发
categories: android界面开发
---
# 简介： #
在Android开发中，经常会碰到一个图标加几个文字的界面，平常的解决方案可能是使用一个线性布局，然后放置图标和文字的位置，其实有一种更简单的方式实现这种布局，并且你可以在它的四个方向上都设置上图标。如下图：

![四周都有图片的TextView](/imgs/textview_draw.png)

# 实现方式： #
## 1. xml文件实现 ，代码如下：##

   ```java
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="这是文字"
        android:textColor="#fff"
        android:drawableStart="@drawable/btc_logo"
        android:drawableTop="@drawable/eth_logo"
        android:drawableEnd="@drawable/btc_logo"
        android:drawableBottom="@drawable/eth_logo"
        android:drawablePadding="10dp"/>

   ```

## 代码解释： ##

> drawableStart：在左边画图标
> 
> drawableTop：在顶部画图标
> 
> drawableEnd：在右边画图标
> 
> drawableBottom：在底部画图标
> 
> drawablePadding：添加文字和图标之间的间距
> 
> 大家可以根据具体需求，选择属性，实现自己想要的效果

## 2.代码实现： ##

1）使用setCompoundDrawables（）方法
> setCompoundDrawables(@Nullable Drawable left, @Nullable Drawable top,
            @Nullable Drawable right, @Nullable Drawable bottom)
> 
> 四个参数分别为左，上，右，下，方位的图标，不想设置，就传null，
> 
> **特别注意一点：在传Drawable值之前，必须先调用Drawable的setBounds方法，类似于这样：left.setBounds(0, 0, left.getIntrinsicWidth(), left.getIntrinsicHeight());**

2）使用setCompoundDrawablesWithIntrinsicBounds（）方法
> setCompoundDrawablesWithIntrinsicBounds(@DrawableRes int left,
            @DrawableRes int top, @DrawableRes int right, @DrawableRes int bottom)
> 
> **四个参数为图片的资源id**

3）使用setCompoundDrawablesWithIntrinsicBounds（）方法
> setCompoundDrawablesWithIntrinsicBounds(@Nullable Drawable left,
            @Nullable Drawable top, @Nullable Drawable right, @Nullable Drawable bottom) 
> 
> **四个参数为图片Drawable,不需要调用Drawable的setBounds方法**

4）使用setCompoundDrawablesRelative系列方法代替以上对应的方法，它们的不同点也只是实现Drawable布局的方式不同而已