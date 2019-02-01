---
title: 搬瓦工VPS搭建PPTP VPN
date: 2016-07-28 14:51:32
tags: VPN
categories: VPN
---


之前一直在用神器Lantern和Nydus(20元一个月)，感觉还不错，但是各有各的不足！于是开始自己动手折腾用VPS搭建VPN，上网逛了一圈，最后选择了搬瓦工的VPS：512RAM，10G SSD，1000G/月，19.9$/年，用了个优惠码，最后用支付宝付了19刀。
<!-- more -->
下面具体介绍下如何创建PPTP VPN:

### 1.进入搬瓦工控制面板界面

点击stop按钮，选择左侧的Install new OS，推荐装Centos 6 x86

![img](https://raw.githubusercontent.com/ckj375/img-folder/master/bandwagon/control_panel.png)

### 2.安装完毕后可以直接选择Root shell-interactive启动命令窗口
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/bandwagon/shell_window.png)

### 3.安装一键PPTP VPN

wget http://www.hi-vps.com/shell/vpn_centos6.sh

sh vpn_centos6.sh

选择1自动安装VPN，安装完成后，会自动生成一个默认的VPN用户名和随机密码。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/bandwagon/install_window.png)

### 4.添加任意VPN账号

sh vpn_centos6.sh

选择3即可手动添加用户名和密码
