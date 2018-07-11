---
title: 一个兼容沉浸式模式的可以从上部和底部弹出的snackbar库
date: 2018-07-11 19:07:51
tags:
  - java
  - android
categories: android开源库
---
# 简介： #
现在越来越多的app使用snackbar，google官方提供的一个提示工具，但是它只能从底部弹出来。但是在实际开发中，往往会和ios使用一套ui，ios流行从顶部弹出提示。为了同ios实现相同的效果，我特别封装了一个可以从上部和底部弹出的snackbar库，并且兼容沉浸模式，大体效果如下图：
![效果动态图](/imgs/topbottomsnackbar.gif)

**github地址：**[https://github.com/wypeng2012/TopBottomSnackBar](https://github.com/wypeng2012/TopBottomSnackBar)

> **注意：使用这个库必须api版本 >= 14**

# 使用方法： #
## 1. 从顶部弹出： ##
要想从顶部弹出，要使用TBSnackbar.STYLE_SHOW_TOP模式，具体代码如下：

```java
/* if you use STYLE_SHOW_TOP and your activity has toolbar or
actionbar ,you should use "findViewById(android.R.id.content)",must
not use "getWindow().getDecorView()"*/
TBSnackbar.make(findViewById(android.R.id.content), "This is a top snack!", TBSnackbar.LENGTH_SHORT, TBSnackbar.STYLE_SHOW_TOP).show();

```
## 2. 从底部弹出： ##
要想从底部弹出，要使用TBSnackbar.STYLE_SHOW_BOTTOM模式，具体代码如下：

```java
// if you use STYLE_SHOW_BOTTOM  ,you can use any view.But if you use
//CoordinatorLayout,you must use CoordinatorLayout.
TBSnackbar.make(findViewById(android.R.id.content), "This is a bottom snack!", TBSnackbar.LENGTH_SHORT,TBSnackbar.STYLE_SHOW_BOTTOM).show();

```
## 3. 使用沉浸模式： ##
使用沉浸模式，肯定是从顶部弹出，要使用TBSnackbar.STYLE_SHOW_TOP_FITSYSTEMWINDOW模式，具体代码如下：

```java
// if you use STYLE_SHOW_TOP_FITSYSTEMWINDOW ,you must use
//getWindow().getDecorView()  android api >= 19        
TBSnackbar.make(getWindow().getDecorView(),"This is a fitsystemwindow snack!", TBSnackbar.LENGTH_SHORT,TBSnackbar.STYLE_SHOW_TOP_FITSYSTEMWINDOW).show();

```
## 4. 添加图标： ##

```java
//setIconLeft(@DrawableRes int drawableRes, float sizeDp) the size is dp,24dp is ok
//if you want change the icon padding you can use setIconPadding(int padding)
//setIconRight(@DrawableRes int drawableRes, float sizeDp) you can use        
TBSnackbar snackbar = TBSnackbar.make(findViewById(android.R.id.content), "This is a left icon snack!", TBSnackbar.LENGTH_SHORT, TBSnackbar.STYLE_SHOW_TOP);
snackbar.setIconLeft(R.mipmap.ic_core,24);
snackbar.show()

```
## 5. 添加action： ##

```java
//you can use it like google's SnackBar 
final TBSnackbar snackbar = TBSnackbar.make(findViewById(android.R.id.content), "This is a action snack!", TBSnackbar.LENGTH_INDEFINITE, TBSnackbar.STYLE_SHOW_TOP);
snackbar.setAction("Action", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        snackbar.dismiss();
                    }
                });
 snackbar.show();

```
# 如何远程依赖： #
## 1. Maven ##

```base
     <dependency>  
          <groupId>com.github</groupId>  
          <artifactId>topbottomsnackbar</artifactId> 
          <version>1.1.0</version> 
          <type>pom</type> 
     </dependency>

```

## 2. Gradle ##

```base
compile 'com.github:topbottomsnackbar:1.1.0'

```
## 3. Ivy ##

```base
<dependency org='com.github' name='topbottomsnackbar' rev='1.1.0'>
       <artifact name='$AID' ext='pom'>
       </artifact>
</dependency>

```