---
title: Android源码解析(二)：Handler
date: 2019-05-24 18:18:33
tags: Handler
categories: Android
---

### 前言
在写这篇博客之前，一直在想网上已经有那么多Handler相关的技术文章了，为啥还要再写一篇呢？读了很多Handler相关的技术文章，发现不同的讲解思路会影响读者的认知，有的人总结的清晰明了，而有的人则容易把人带入误区，所以我决定按自己的思路写一篇关于Handler的博客，尽可能的简洁明了，希望对自己以及他人有所帮助。

<!-- more -->

### 目录    
1.Handler的作用    
2.Handler消息机制原理    
-2.1MessageQueue和Looper的关系？    
-2.2 Handler如何发送消息？    
-2.3 Handler如何接收消息？    
-2.4 Handler消息机制原理图

### Handler的作用
Handler主要用于线程间通信。

举个例子：
![img](https://github.com/ckj375/img-folder/blob/master/android-handler/pic01.png?raw=true)
在子线程中执行耗时操作，当执行完毕后需要通知主线程刷新UI，那么问题来了，子线程怎么去通知主线程呢？这时我们就可以借助Handler，首先在主线程中创建一个Handler对象，当子线程执行完耗时操作后，在子线程中调用刚才创建的Handler对象发送消息，随后在主线程中通过Handler对象的handleMessage回调方法去处理消息。
```
// 主线程中创建Handler对象，并通过回调方法接收处理消息
private Handler mHandler = new Handler() {
      @Override
      public void handleMessage(Message msg) {
          // 处理消息
      }
};
```
```
// 子线程中通过Handler对象发送消息
new Thread(new Runnable() {
    @Override
     public void run() {
         // 执行耗时操作...
         Message msg = new Message();
         mHandler.sendMessage(msg);
     }
}).start();
```

### Handler消息机制原理
Handler消息机制涉及到几个重要的类，分别是Looper（消息循环器），MessageQueue（即消息队列，用于存放消息，它基于单链表的数据结构）和Message。简单概述就是Handler对象发送Message给MessageQueue，Looper从MessageQueue中取出Message交还给Handler对象处理。
#### MessageQueue和Looper的关系？
MessageQueue是在Looper中创建的，创建Looper对象时会同时创建一个MessageQueue对象（注：主线程中无需手动创建Looper对象，系统已为我们自动创建Looper对象并开启消息循环），由此可知Looper对象和MessageQueue对象是一对一的关系。
```
// Looper构造器
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```
Looper用于开启消息循环，检测MessageQueue中是否有消息，若有消息则将消息依次分发出去，若没有消息则阻塞直到有消息过来为止。一个线程对应一个Looper，一个Looper对应一个MessageQueue。创建Handler对象时会通过Looper.myLooper()方法获取当前线程所对应的Looper对象，若未获取到Looper对象则会抛出运行时异常，所以在子线程中创建Handler对象时须手动创建Looper对象并开启消息循环。
```
// Handler构造器
public Handler() {
    this(null, false);
}
public Handler(Callback callback, boolean async) {
    if (FIND_POTENTIAL_LEAKS) {
        final Class<? extends Handler> klass = getClass();
        if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                (klass.getModifiers() & Modifier.STATIC) == 0) {
            Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                klass.getCanonicalName());
        }
    }
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```
举个例子，如果需要在子线程中弹出Toast，则代码如下：
```
new Thread(new Runnable() {
        public void run() {
        Looper.prepare();// 手动创建Looper对象
        Toast.makeText(…).show();// Toast的创建依赖于创建Handler对象
        Looper.loop();// 开启消息循环
}
```
通过分析源码我们可以看到当调用Looper.prepare()后会创建一个Looper对象，并和当前线程进行绑定，若当前线程已经绑定了Looper对象，则不会重复创建Looper对象，所以说一个线程对应一个Looper对象。
```
public static void prepare() {
       prepare(true);
}
private static void prepare(boolean quitAllowed) {
       if (sThreadLocal.get() != null) {
           throw new RuntimeException("Only one Looper may be created per thread");
       }
       sThreadLocal.set(new Looper(quitAllowed));
}
```
#### Handler如何发送消息？

![img](https://github.com/ckj375/img-folder/blob/master/android-handler/pic02.png?raw=true)
Handler发送Message给MessageQueue，我们以sendMessage(Message msg)为例，经过一系列的方法最终会走到enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis)方法，将消息插入到消息队列中，按发送的时间大小进行排列，与此同时，会通过msg.target = this将当前Handler对象赋给Message对象。
```
public final boolean sendMessage(Message msg)
    {
        return sendMessageDelayed(msg, 0);
}
...
...
...
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        if (mAsynchronous) {
            msg.setAsynchronous(true);
        }
        return queue.enqueueMessage(msg, uptimeMillis);
    }
```
#### Handler如何接收消息？
通过调用Looper.loop()开启消息循环后，会依次取出消息队列中的Message，并调用msg.target.dispatchMessage(msg)，这个target前面有提到，就是Handler对象自身，Handler在发送消息时会将自身引入到Message中。这下我们就明白了，消息时通过Handler的dispatchMessage(msg)方法进行分发，然后通过Handler的回调方法handleMessage(msg)去处理消息。
#### Handler消息机制流程图
最后我们再来看一下整个消息机制的流程图：
![img](https://github.com/ckj375/img-folder/blob/master/android-handler/pic03.png?raw=true)
