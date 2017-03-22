---
title: 面试题
date: 2017-03-21 12:03:50
tags: [面试,interview]
categories: Other
description: 本文总结了平常面试可能会遇到的面试题，会不断完善，请读者注意发布时间，文章不保证失效性，在版本更新后可能会有所差异

---


<!--more-->
1. [Activity的生命周期(Fragment的生命周期)](#1)2. [数据储存方式](#2)3. [Handler机制和分析](#3)4. [Asynctask优缺点(分析)](#4)5. [对比以上两种优缺点](#5)6. [Service的概念](#6)7. [Service保活](#7)8. [自定义View](#8)9. [进程](#9)10. [Context](#10)11. [内存泄露和预防](#11)12. [数据解析](#12)13. [事件分发机制](#13)14. [webview使用](#14)15. [数据传输的加密](#15)16. [serfaceview](#16)17. [ANR的概念和避免](#17)18. [FC](#18)19. [性能优化 ](#19)20. [动态加载(插件化)](#20)21. [热修复 ](#21)22. [异常捕获和处理](#22)23. [AIDL的方式(多线程间通信和多进程之间通信有什么不同)](#23)24. [安卓系统架构](#24)25. [Android程序运行时权限与文件系统权限](#25)26. [适配(屏幕和系统)](#26)27. [动画*](#27)28. [Other](#28)


## <a id="1" name="1">Activity的生命周期(Fragment的生命周期)</a>

### 什么是Activity的 “生命周期”

运行中的应用程序分为五大类，分别是：

* 前景模式：foreground process

* 可见模式：visible process

* 背景模式：background process

* 空白模式：empty process

* 服务模式：service process


除了最后一个，貌似service process是Service的事情了。其他都与Activity相关。

Android系统会判断应用程序Activity是属于哪一个类，给予不同的Activity生命周期。 

![Activity生命周期](/img/activityshengmingzhouqi.gif)

>activity包含七个生命周期的方法：

>（1）onCreate()：创建时被调用

>（2）onStart():可见时被调用

>（3）onRestart():重新可见时被调用，接着会调用onStart()

>（4）onResumed():activity获得焦点，可进行输入时被调用

>（5）onPaused():失去焦点但可见时被调用（在其他应用需要内存时，可能会被kills）

>（6）onStopped():完全不可见时被调用，会被系统kills

>（7）onDestroyed():被销毁时被调用，会被系统kills

### 被调用的情况
1.启动Activity：系统会先调用onCreate方法，然后调用onStart方法，最后调用onResume，Activity进入运行状态。

2.当前Activity被其他Activity覆盖其上或被锁屏：系统会调用onPause方法，暂停当前Activity的执行。

3.当前Activity由被覆盖状态回到前台或解锁屏：系统会调用onResume方法，再次进入运行状态。

4.当前Activity转到新的Activity界面或按Home键回到主屏，自身退居后台：系统会先调用onPause方法，然后调用onStop方法，进入停滞状态。

5.用户后退回到此Activity：系统会先调用onRestart方法，然后调用onStart方法，最后调用onResume方法，再次进入运行状态。

6.当前Activity处于被覆盖状态或者后台不可见状态，即第2步和第4步，系统内存不足，杀死当前Activity，而后用户退回当前Activity：再次调用onCreate方法、onStart方法、onResume方法，进入运行状态。

7.用户退出当前Activity：系统先调用onPause方法，然后调用onStop方法，最后调用onDestory方法，结束当前Activity。
但是知道这些还不够，我们必须亲自试验一下才能深刻体会，融会贯通。

8*.在Activity被其他非全屏Activity（Theme属性使用了dialog主题）覆盖时，会调用onPause

### 保存Activity状态
当我们的activity开始Stop，系统会调用 onSaveInstanceState()，Activity可以用键值对的集合来保存状态信息。这个方法会默认保存Activity视图的状态信息，如在 EditText 组件中的文本或 ListView的滑动位置。

为了给Activity保存额外的状态信息，你必须实现onSaveInstanceState()并增加key-value pairs到 Bundle 对象中。

```java
static final String STATE_SCORE = "playerScore";
static finalString STATE_LEVEL = "playerLevel";
...
@Override
public void onSaveInstanceState(Bundle 
savedInstanceState) {
    // Save the user's current game state
   savedInstanceState.putInt(STATE_SCORE, mCurrentScore);
   savedInstanceState.putInt(STATE_LEVEL, mCurrentLevel);
    // Always call thesuperclass so it can save the view hierarchy state
    super.onSaveInstanceState(savedInstanceState);

}
```>Caution: 必须要调用 onSaveInstanceState()方法的父类实现，这样默认的父类实现才能保存视图状态的信息。### 更多请参考 
>[http://developer.android.com/reference/android/app/Activity.html](http://developer.android.com/reference/android/app/Activity.html)

>[http://developer.android.com/training/basics/activity-lifecycle/starting.html](http://developer.android.com/training/basics/activity-lifecycle/starting.html)

>[http://developer.android.com/training/basics/activity-lifecycle/pausing.html](http://developer.android.com/training/basics/activity-lifecycle/pausing.html)

>[http://developer.android.com/training/basics/activity-lifecycle/stopping.html](http://developer.android.com/training/basics/activity-lifecycle/stopping.html)

>[http://developer.android.com/training/basics/activity-lifecycle/recreating.html](http://developer.android.com/training/basics/activity-lifecycle/recreating.html)

>[http://developer.android.com/guide/components/activities.html](http://developer.android.com/guide/components/activities.html)

>[一个帖子学会Android四大组件](http://www.apkbus.com/android-18204-1-1.html)

>[两分钟明白Activity生命周期](http://www.apkbus.com/blog-99192-39550.html)


	

## <a id="2" name="2">数据储存方式</a>

SQLite：SQLite是一个轻量级的数据库，支持基本的SQL语法，是常被采用的一种数据存储方式。 Android为此数据库提供了一个名为SQLiteDatabase的类，封装了一些操作数据库的api

SharedPreference： 以键值对的形势储存。其本质就是一个xml文件，常用于存储较简单的参数设置。

File： 即常说的文件（I/O）存储方法，常用语存储大数量的数据，但是缺点是更新数据将是一件困难的事情。

ContentProvider: Android系统中能实现所有应用程序共享的一种数据存储方式，由于数据通常在各应用间的是互相私密的，所以此存储方式较少使用，但是其又是必不可少的一种存储方式。例如音频，视频，图片和通讯录，一般都可以采用此种方式进行存储。每个Content Provider都会对外提供一个公共的URI（包装成Uri对象），如果应用程序有数据需要共享时，就需要使用Content Provider为这些数据定义一个URI，然后其他的应用程序就通过Content Provider传入这个URI来对数据进行操作。

## <a name="3" id="3">Handler机制和分析</a>
