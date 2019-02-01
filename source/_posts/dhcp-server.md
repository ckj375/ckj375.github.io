---
title: Android电视盒子联调技能(一)：使用DHCP Server
date: 2017-04-20 11:34:28
tags: dhcp
categories: 技术
---

最近在东方有线机房联调碰到个问题，东方有线的Android电视盒子是用同轴线接入封闭内网的，笔记本无法访问内网，所以就无法连接Android电视盒子。还好Android电视盒子预留了一个网口，所以就通过DHCP Server搭建一个局域网，给Android电视盒子分配一个ip，这样笔记本就可以连上Android电视盒子进行调试了。首先需要手动设置以太网网卡IPV4地址为192.168.10.1，子网掩码为255.255.255.0
<!-- more -->

打开dhcpwiz.exe，选择本地连接，下一步；
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic01.png)  

勾选HTTP (Web Server)，下一步；  
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic02.png)  

修改IP-Pool从192.168.10.2开始，下一步；  
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic03.png)  

勾选Overwrite existing file，点击"Write INI file"按钮写入配置文件，下一步；  
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic04.png)  

依次点击Configure，Install，start，勾选Run DHCP server immediatly，完成。  
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic05.png)  

点击Continue as try app按钮后，DHCP服务启动。  
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic06.png)  

将Android盒子关机并用网线连接笔记本，随后将Android盒子开机，即可获取到ip，可登陆 http://127.0.0.1/dhcpstatus.xml 查看局域网ip，随后打开命令终端，输入adb connect ip（注：Windows 10下需指定端口号：adb connect ip:port）即可连接Android盒子，此时再插上东方有线的同轴线即可进行内网调试。
![img](https://raw.githubusercontent.com/ckj375/img-folder/master/dhcp-server/pic07.png)
