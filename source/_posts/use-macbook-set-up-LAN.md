---
title: Android电视盒子联调技能(二)：使用MacBook为设备分配IP
date: 2017-07-28 10:38:04
tags: Mac OS
categories: 技术
---
之前介绍了在Windows环境下如何通过DHCP Server为Android电视盒子分配ip地址。现在介绍下MAC OS下如何实现ip地址分配。（背景介绍:东方有线的Android电视盒子是用同轴线接入封闭内网的（无Wifi功能），笔记本无法访问内网，所以就无法连接Android电视盒子。还好Android电视盒子预留了一个网口，可以通过笔记本搭建一个局域网，给Android电视盒子分配一个ip，这样笔记本就可以连上Android电视盒子进行调试了。）<!-- more -->
首先将MacBook连上USB网口转换器，接着进入设置页面，选择网络。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/use-macbook-set-up-LAN/pic01.png)
选择网口转换器对应的以太网(AX88772A)，手动设置IPV4地址为192.168.10.1，子网掩码为255.255.255.0，点击“应用”按钮。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/use-macbook-set-up-LAN/pic02.png)
回到设置页面，选择共享。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/use-macbook-set-up-LAN/pic03.png)
设置共享来源和端口，均为刚刚设置的以太网(AX88772A)，然后选中左侧的“互联网共享”选项。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/use-macbook-set-up-LAN/pic04.png)
最后用网线连接MacBook和Android电视盒子，此时Android电视盒子即可获取ip地址（若获取失败，尝试重启MacBook）。打开命令终端输入：arp -a 即可查看所有与MacBook通信的ip地址（192.168.2.2即Android电视盒子当前ip）。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/use-macbook-set-up-LAN/pic05.png)
