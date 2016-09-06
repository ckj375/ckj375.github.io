---
title: Android内存泄露总结
date: 2016-05-10 08:54:32
tags: 内存泄露
categories: Android
---

#### 前言
　　不少人认为JAVA程序，因为有垃圾回收机制，应该没有内存泄露。
其实如果我们一个程序中，已经不再使用某个对象，但是因为仍然有引用指向它，垃圾回收器就无法回收它，当然该对象占用的内存就无法被使用，这就造成了内存泄露。如果我们的java运行很久,而这种内存泄露不断的发生，最后就没内存可用了。当然java的，内存泄漏和C/C++是不一样的。如果java程序完全结束后，它所有的对象就都不可达了，系统就可以对他们进行垃圾回收，它的内存泄露仅仅限于它本身，而不会影响整个系统的。
　　Android的一个应用程序的内存泄露对别的应用程序影响不大。为了能够使得Android应用程序安全且快速的运行，Android的每个应用程序都会使用一个专有的Dalvik虚拟机实例来运行，它是由Zygote服务进程孵化出来的，也就是说每个应用程序都是在属于自己的进程中运行的。Android为不同类型的进程分配了不同的内存使用上限，如果程序在运行过程中出现了内存泄漏的而造成应用进程使用的内存超过了这个上限，则会被系统视为内存泄漏，从而被kill掉，这使得仅仅自己的进程被kill掉，而不会影响其他进程（如果是system_process等系统进程出问题的话，则会引起系统重启）。
## 一.内存泄漏常见原因
* 1.查询数据库没有关闭游标
* 2.构造 Adapter时，没有使用缓存的convertView
* 3.Bitmap对象不在使用时调用recycle()释放内存
* 4.释放对象的引用

## 二.内存泄漏检测方法
检测工具：
* 1.Android Studio
* 2.adb命令
```
adb shell dumpsys meminfo 包名
adb shell dumpsys meminfo -oom 查看当前手机所有进程的内存消耗情况
adb shell top|grep 包名
```

测试方法：
* 1.手动随机测试或指定路径压力测试    
* 2.利用第三方工具进行制定路径压力测试：按键精灵  
* 3.monkey测试：adb shell monkey -p com.fineos.camera -v 500  （发送500条伪随机事件）

以Android Studio为例：  
1.打开应用，不做任何操作时的内存占用  
2.操作一段时间后，内存占用不断上升  
3.停止操作，静置一段时间，内存仍未被释放  
4.手动调用一次GC后，点击dump java heap按钮  
5.dump成功后会自动打开hprof文件,这个文件会保存在项目根目录下的captures文件夹中，由于Android studio自带的界面查找内存泄漏不是很智能，所以建议使用第三方工具MAT，由于MAT不识别此文件，需右键Export to standard .hprof，然后用MAT打开导出的hprof(File->Open heap dump)

## 三.内存分析工具 MAT
　　如果使用检测工具确实发现了我们的程序中存在内存泄漏，那又如何定位到具体出现问题的代码片段，最终找到问题所在呢？如果从头到尾的分析代码逻辑，那肯定会把人逼疯，特别是在维护别人写的代码的时候。这里介绍一个极好的内存分析工具 -- Memory Analyzer Tool(MAT) 。  
　　MAT 是一个 Eclipse 插件，同时也有单独的RCP 客户端。官方下载地址、 MAT 介绍和详细的使用教程请参见：www.eclipse.org/mat，在此不进行说明了。另外在 MAT 安装后的帮助文档里也有完备的使用教程。在此仅举例说明其使用方法。我自己使用的是 MAT 的 eclipse 插件，使用插件要比RCP 稍微方便一些。  

使用MAT 进行内存分析需要几个步骤，包括：生成 .hprof 文件、打开MAT 并导入.hprof文件、使用 MAT 的视图工具分析内存。  
(a) 生成.hprof文件  
(b) 使用 MAT导入.hprof文件  
* 1 如果是 eclipse 自动生成的.hprof 文件，可以使用 MAT 插件直接打开（可能是比较新的 ADT才支持）；
* 2 如果eclipse 自动生成的 .hprof 文件不能被MAT 直接打开， 或者是使用 android.os.Debug.dumpHprofData()方法手动生成的.hprof 文件，则需要将 .hprof 文件进行转换，转换的方法：
例如我将 .hprof 文件拷贝到PC 上的/ANDROID_SDK/tools 目录下，并输入命令 hprofconv xxx.hprof yyy.hprof，其中xxx.hprof 为原始文件， yyy.hprof 为转换过后的文件。转换过后的文件自动放在 /ANDROID_SDK/tools 目录下。OK ，到此为止， .hprof 文件处理完毕，可以用来分析内存泄露情况了。
* 3 在Eclipse 中点击Windows->Open Perspective->Other->Memory Analyzer，或者打Memory Analyzer Tool 的RCP。在 MAT 中点击 File->Open File，浏览并导入刚刚转换而得到的 .hprof文件。  

(c) 使用 MAT的视图工具分析内存
导入.hprof 文件以后， MAT 会自动解析并生成报告，点击 Dominator Tree，并按Package 分组，选择自己所定义的 Package 类点右键，在弹出菜单中选择 List objects->With incoming
references。这时会列出所有可疑类，右键点击某一项，并选择 Path to GC Roots -> exclude weak/soft references，会进一步筛选出跟程序相关的所有有内存泄露的类。据此，可以追踪到代码中的某一个产生泄露的类。  

具体的分析方法在此不做说明了，因为在 MAT 的官方网站和客户端的帮助文档中有十分详尽的介绍。
了解MAT 中各个视图的作用很重要，例如 www.eclipse.org/mat/about/screenshots.php 中介绍的。
总之使用 MAT 分析内存查找内存泄漏的根本思路，就是找到哪个类的对象的引用没有被释放，找到没有被释放的原因，也就可以很容易定位代码中的哪些片段的逻辑有问题了。

## 四.第三方开源库LeakCanary
LeakCanary是一个开源的检测内存泄露的java库。
如何集成LeakCanary?  
In your build.gradle:
```
dependencies {
  debugCompile 'com.squareup.leakcanary:leakcanary-android:1.4-beta2'
  releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2'
  testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2'
}
```

In your Application class:
```
public class ExampleApplication extends Application {

  @Override public void onCreate() {
  super.onCreate();
  LeakCanary.install(this);
  }
}
```

加入上述代码后，在你的debug版本中如果Activity有内存泄漏，通知栏会自动弹出内存泄漏通知。

更详细的使用教程可以去Github搜LeakCanary：https://github.com/square/leakcanary/wiki/FAQ

## 五.内存泄漏总结
通过对比MAT和LeakCanary发现，LeakCanary可以很方便的检测出内存泄露的路径，尤其是Context的泄漏，但是对于某些自定义的类或对象，需手动监听，项目初期集成LeakCanary会避免很多内存泄露的情形；
而MAT则主要列出可疑的类及对象，不能精确定位，需对项目代码熟悉才行。

[阅读原文](http://ckj375.github.io/2016/05/10/Android%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%80%BB%E7%BB%93/)
