---
title: Ubuntu下安装配置zsh和oh my zsh
date: 2016-07-15 17:09:36
tags: Zsh
categories: Ubuntu
---

zsh优势：自动补全功能强大和很高的可配置性
------
1. 查看当前系统装了哪些shell
   cat /etc/shells<!-- more -->
2. 当前正在运行的是哪个版本的shell
   echo $SHELL
3. 安装zsh
   sudo apt-get zsh
4. 切换zsh
   chsh -s /bin/bash(非实时，需重启)
5. 安装oh my zsh
   wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
6. 设置zsh的参数
   安装完zsh后，在home目录下会有一个名为.zshrc的隐藏文件，可以根据个人习惯配置zsh的参数
   oh my zsh 在安装时已经自动读取当前的环境变量并进行了设置，你可以继续追加其他环境变量

   oh my zsh各式各样的主题预览：https://github.com/robbyrussell/oh-my-zsh/wiki/themes
