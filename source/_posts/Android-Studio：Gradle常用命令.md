---
title: Android Studio：Gradle常用命令
date: 2016-07-19 15:33:51
tags: Android Studio
categories: Android Studio
---

Android Studio中自带Terminal，可以直接使用gradle命令，不用另开命令窗口，相当方便，下面总结一下常用的命令：
* 1.查看Gradle版本号
```
./gradlew -v
```
* 2.清除app目录下的build文件夹
```
./gradlew clean
```
* 3.检查依赖并编译打包
```
./gradlew build
```
* 4.编译并打Debug包
```
./gradlew assembleDebug
```
* 5.编译并打Release包
```
./gradlew assembleRelease
```
* 6.结合productFlavors可以编译指定Flavor的debug或release版本
```
./gradlew assembleFlavor     
./gradlew assembleFlavorDebug
./gradlew assembleFlavorRelease
```
[阅读原文](http://ckj375.github.io/2016/07/19/Android-Studio%EF%BC%9AGradle%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/)
