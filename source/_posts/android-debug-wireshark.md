---
title: Android调试技巧之Wireshark抓包
date: 2017-12-20 17:07:56
tags: [Wireshark,网络请求,Android调试]
categories: Android
---
在Android网络请求调试过程中经常会碰到各种各样的问题，此时就需要通过抓取网络请求包来分析定位问题，通过网络请求包可以知晓请求头，请求参数，服务器状态响应码及响应内容等。抓取网络请求包的方式有多种，今天主要介绍网络抓包利器：Wireshark。

### Wireshark抓取网络包
打开Wireshark，点击菜单“捕获选项”按钮，弹出对话框如下，选中对应的网卡，然后点击“开始”按钮, 开始抓包。（注：Wireshark是捕获机器上某一块网卡的网络包，当机器上有多块网卡的时候，需要选择一个网卡）
<!-- more -->

![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic0.jpg)

开始抓包后，Wireshark便不停地捕获对应网卡上的网络包，此时打开Android模拟器并安装demo应用进行网络请求操作即可抓取网络请求包，点击Wireshark左上角的红色停止按钮停止抓包。此时Wireshark界面如下，主要由3部分构成：封包列表，封包详细信息和16进制数据。

![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic2.jpg)

### Wireshark过滤规则  
抓取网络包后，如何在海量数据中筛选出需要的信息呢，这时就需要用到过滤器。以我的demo应用为例:  
```
承载协议：HTTP + POST
接口地址：http://112.4.10.122:8090/HDC/1.0/hop/svc/pay/toPay.ajax  
```
1. 协议过滤  
如过滤http，直接在过滤器中输入http即可。
2. ip过滤  
过滤源ip或目的ip。在Wireshark过滤器中输入过滤条件，如查找目的地址为112.4.10.122的包，ip.dst==112.4.10.122；查找源地址为ip.src==10.200.52.66
3. 端口过滤  
如过滤8090端口，在过滤器中输入tcp.port==8090，这条规则是把源端口和目的端口为8090的都过滤出来。使用tcp.dstport==8090只过滤目的端口为80的包，tcp.srcport==8090只过滤源端口为8090的包。
4. Http模式过滤  
如过滤get包，http.request.method=="GET",过滤post包，http.request.method=="POST"
5. 逻辑运算符and/or  
过滤两种条件时，使用and连接，如过滤ip为192.168.101.8并且为http协议的，ip.src==192.168.101.8 and http

![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic3.jpg)

### 网络包分析
在封包列表界面点击需要分析的封包，即可显示该封包的详细信息。

请求包信息如下：

![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic4.jpg)

响应包信息如下：

![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic5.jpg)
