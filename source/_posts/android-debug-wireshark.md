---
title: Android调试技巧之Wireshark抓包
date: 2017-12-20 17:07:56
tags: [Wireshark,网络请求,Android调试]
categories: Android
---
在Android网络请求开发过程中经常会碰到各种各样的问题，比如请求头的设置，请求参数的传递方式，服务器状态响应码及响应内容。今天就介绍下网络抓包利器：Wireshark。
### Wireshark抓取网络包
打开Wireshark，点击菜单“捕获选项”按钮，弹出对话框如下，选中对应的网卡，然后点击“开始”按钮, 开始抓包。（注：wireshark是捕获机器上的某一块网卡的网络包，当机器上有多块网卡的时候，需要选择一个网卡）
<!-- more -->
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic1.jpg)

开始抓包后，Wireshark便不停地捕获对应网卡上的网络包，此时在Android模拟器上安装demo应用，并进行网络请求操作即可抓取APP网络请求包，点击Wireshark左上角的红色停止按钮停止抓包。此时Wireshark界面如下，主要由3部分构成：封包列表，封包详细信息和16进制数据。
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic2.jpg)
1. Display Filter(显示过滤器)，  用于过滤
2. Packet List Pane(封包列表)， 显示捕获到的封包， 有源地址和目标地址，端口号。 颜色不同，代表
3. Packet Details Pane(封包详细信息), 显示封包中的字段
4. Dissector Pane(16进制数据)
5. Miscellanous(地址栏，杂项)

### WireShark过滤规则  
抓取网络包后，如何在海量数据中筛选出需要的信息呢，这时就需要用到过滤器。以我的Demo应用为例，应用中请求的接口地址是：http://112.4.10.122:8090/HDC/1.0/hop/svc/pay/toPay.ajax，请求方式POST。
1.协议过滤
如过滤http，直接在过滤器中输入http即可。
2.ip过滤
过滤源ip或目的ip。在wireshark的过滤器中输入过滤条件，如查找目的地址为112.4.10.122的包，ip.dst==112.4.10.122；查找源地址为ip.src==10.200.52.66；
3.端口过滤
端口过滤。如过滤8090端口，在过滤器中输入tcp.port==8090，这条规则是把源端口和目的端口为8090的都过滤出来。使用tcp.dstport==8090只过滤目的端口为80的，tcp.srcport==8090只过滤源端口为8090的包；
4.Http模式过滤
http模式过滤。如过滤get包，http.request.method=="GET",过滤post包，http.request.method=="POST"；
5.逻辑运算符and / or
连接符and的使用。过滤两种条件时，使用and连接，如过滤ip为192.168.101.8并且为http协议的，ip.src==192.168.101.8 and http。
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic3.jpg)
### 网络包分析
查看请求信息：请求头和请求参数
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic4.jpg)
查看响应信息
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/android-debug-wireshark/pic5.jpg)
