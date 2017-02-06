---
title: 如何在Android源码中为APK签系统签名？
date: 2016-07-20 16:42:19
tags: Android签名
categories: Android
---
&#160; &#160; &#160; &#160; 做Android系统项目时经常会用基线代码编译出来的APK push到定制版本的手机里去验证,但
往往会碰到签名不一致导致APK在桌面不显示的问题,这时候就需要用对应源码对APK重新签名打包.
1. 找到系统签名文件,文件路径：在源码的\build\target\product\security目录下有platform.x509pem和platform.pk8两个文件
<!-- more -->
2. 把签名文件复制到\out\host\linux-x86\framework目录下

3. 把预签名的apk复制到\out\host\linux-x86\framework目录下

4. 把编译路径切换到\out\host\linux-x86\framework目录下，使用java -jar signapk.jar platform.x509.pem platform.pk8 in.apk(预签名的apk) out.apk(签名之后的apk)
