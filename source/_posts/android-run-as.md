---
title: 未ROOT设备上查看Android应用沙盒数据
date: 2017-02-14 14:09:44
tags: run-as
categories: Android
---

最近碰到一个问题，需要在Android盒子上抓取一份日志，位于data/data/packageName下，开始敲命令：
```
chenkaijian@chenkaijian-ubuntu ~ $ adb connect 10.200.52.122
connected to 10.200.52.122:5555
chenkaijian@chenkaijian-ubuntu ~ $ adb shell
shell@p200:/ $ cd data
shell@p200:/data $ cd data
shell@p200:/data/data $ ls
opendir failed, Permission denied
255|shell@p200:/data/data $
```
<!-- more -->
问题来了，我的盒子没有root，所以没有权限访问应用沙盒数据。这可怎么办？幸好Android提供了run-as命令。
```
chenkaijian@chenkaijian-ubuntu ~ $ adb shell
shell@p200:/ $ run-as com.pptv.tvsports
shell@p200:/data/data/com.pptv.tvsports $ ls
app_libs
app_webview
asserts
cache
databases
files
lib
shared_prefs
shell@p200:/data/data/com.pptv.tvsports $
```
接下来就可以用直接在命令行查看对应的文件了，需要注意的是，此方法只适用于Debug应用。
```
shell@p200:/data/data/com.pptv.tvsports/cache $ vi PeerLog
```
