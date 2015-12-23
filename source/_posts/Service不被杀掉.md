title: Android开发之如何保证Service不被杀掉（broadcast+system/app）
date: 2014-10-31 16:31:45
tags: android
description: service始终保持在后台运行
---
### 序言
> 最近项目要实现这样一个效果：运行后，要有一个service始终保持在后台运行，不管用户作出什么操作，都要保证service不被kill，这可真是一个难题。参考了现今各种定制版的系统和安全厂商牛虻软件，如何能保证自己的Service不被杀死呢？
其实除了常规的手段，我们可以参考一下微信和360，设置-程序-正在运行，可以看到微信是同时开启了两个进程和服务：
【有兴趣可以研究一下 守护进程 和 AIDL 】


![img](/img/20140329145202078.jpg)

我猜想它应该是相互监听，如果有一方被kill掉，另一个捕获到立即启动，以达到service永远都在运行的状态，貌似360也是这个原理，具体是不是这个样子，还有待参考，目前我还没有参透它们是如何实现的，先简单说一下我自己的防控措施吧，首先介绍一下Service概念，记性不好，重复造一下车轮，高手可以直接看最后。


### Service简介
Service是在一段不定的时间运行在后台，不和用户交互应用组件。每个Service必须在manifest中 通过<service>来声明。可以通过contect.startservice和contect.bindserverice来启动。和其他的应用组件一样，运行在进程的主线程中。这就是说如果service需要很多耗时或者阻塞的操作，需要在其子线程中实现（或者用系统提供的IntentService，它继承了Service，它处理数据是用自身新开的线程）。【当然你也可以在新的线程中startService，这样Service就不是在MainThread了】

#### 本地服务 Local Service 用于应用程序内部
它可以启动并运行，直至有人停止了它或它自己停止。在这种方式下，它以调用Context.startService()启动，而以调用Context.stopService()结束。它可以调用Service.stopSelf() 或 Service.stopSelfResult()来自己停止。不论调用了多少次startService()方法，你只需要调用一次stopService()来停止服务。
【用于实现应用程序自己的一些耗时任务，比如查询升级信息，并不占用应用程序比如Activity所属线程，而是单开线程后台执行，这样用户体验比较好】

#### 远程服务 Remote Service 用于android系统内部的应用程序之间
它可以通过自己定义并暴露出来的接口进行程序操作。客户端建立一个到服务对象的连接，并通过那个连接来调用服务。连接以调用Context.bindService()方法建立，以调用 Context.unbindService()关闭。多个客户端可以绑定至同一个服务。如果服务此时还没有加载，bindService()会先加载它。
【可被其他应用程序复用，比如天气预报服务，其他应用程序不需要再写这样的服务，调用已有的即可】


#### 1. Service的生命周期
![img](http://img.blog.csdn.net/20140329145620343?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFkMTk4OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 2. Service运行方式
以startService()启动服务，系统将通过传入的Intent在底层搜索相关符合Intent里面信息的service。如果服务没有启动则先运行onCreate，然后运行onStartCommand （可在里面处理启动时传过来的Intent和其他参数），直到明显调用stopService或者stopSelf才将停止Service。无论运行startService多少次，只要调用一次stopService或者stopSelf，Service都会停止。使用stopSelf(int)方法可以保证在处理好intent后再停止。onStartCommand ，在2.0后被引入用于service的启动函数，2.0之前为public void onStart(Intent intent, int startId) 。
以bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止。onBind()只有采用Context.bindService()方法启动服务时才会回调该方法。该方法在调用者与服务绑定时被调用，当调用者与服务已经绑定，多次调用Context.bindService()方法并不会导致该方法被多次调用。采用Context.bindService()方法启动服务时只能调用onUnbind()方法解除调用者与服务解除，服务结束时会调用onDestroy()方法。

#### 3. 拥有service的进程具有较高的优先级
官方文档告诉我们，Android系统会尽量保持拥有service的进程运行，只要在该service已经被启动(start)或者客户端连接(bindService)到它。当内存不足时，需要保持，拥有service的进程具有较高的优先级。
1． 如果service正在调用onCreate,onStartCommand或者onDestory方法，那么用于当前service的进程则变为前台进程以避免被killed。
2． 如果当前service已经被启动(start)，拥有它的进程则比那些用户可见的进程优先级低一些，但是比那些不可见的进程更重要，这就意味着service一般不会被killed.
3． 如果客户端已经连接到service (bindService),那么拥有Service的进程则拥有最高的优先级，可以认为service是可见的。
#### 4.  如果service可以使用startForeground(int, Notification)方法来将service设置为前台状态，那么系统就认为是对用户可见的，并不会在内存不足时killed。
#### 5. 如果有其他的应用组件作为Service,Activity等运行在相同的进程中，那么将会增加该进程的重要性。

###保证service不被杀掉

onStartCommand方法，返回START_STICKY

StartCommond几个常量参数简介：
1、**START_STICKY**
在运行onStartCommand后service进程被kill后，那将保留在开始状态，但是不保留那些传入的intent。不久后service就会再次尝试重新创建，因为保留在开始状态，在创建     service后将保证调用onstartCommand。如果没有传递任何开始命令给service，那将获取到null的intent。
2、**START_NOT_STICKY**
在运行onStartCommand后service进程被kill后，并且没有新的intent传递给它。Service将移出开始状态，并且直到新的明显的方法（startService）调用才重新创建。因为如果没有传递任何未决定的intent那么service是不会启动，也就是期间onstartCommand不会接收到任何null的intent。
3、**START_REDELIVER_INTENT**
在运行onStartCommand后service进程被kill后，系统将会再次启动service，并传入最后一个intent给onstartCommand。直到调用stopSelf(int)才停止传递intent。如果在被kill后还有未处理好的intent，那被kill后服务还是会自动启动。因此onstartCommand不会接收到任何null的intent。


``` java
@Override  
public int onStartCommand(Intent intent, int flags, int startId) {  
    flags = START_STICKY;  
    return super.onStartCommand(intent, flags, startId);  
}  
```
> 【结论】 手动返回START_STICKY，亲测当service因内存不足被kill，当内存又有的时候，service又被重新创建，比较不错，但是不能保证任何情况下都被重建，比如进程被干掉了....

####　提升service优先级

在AndroidManifest.xml文件中对于intent-filter可以通过android:priority = "1000"这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，同时适用于广播。
``` java
<service  
    android:name="com.dbjtech.acbxt.waiqin.UploadService"  
    android:enabled="true" >  
    <intent-filter android:priority="1000" >  
        <action android:name="com.dbjtech.myservice" />  
    </intent-filter>  
</service>  
```
> 【结论】目前看来，priority这个属性貌似只适用于broadcast，对于Service来说可能无效

#### 提升service进程优先级

Android中的进程是托管的，当系统进程空间紧张的时候，会依照优先级自动进行进程的回收。Android将进程分为6个等级,它们按优先级顺序由高到低依次是:
   1. 前台进程( FOREGROUND_APP)
   2. 可视进程(VISIBLE_APP )
   3. 次要服务进程(SECONDARY_SERVER )
   4. 后台进程 (HIDDEN_APP)
   5. 内容供应节点(CONTENT_PROVIDER)
   6. 空进程(EMPTY_APP)
当service运行在低内存的环境时，将会kill掉一些存在的进程。因此进程的优先级将会很重要，可以使用startForeground 将service放到前台状态。这样在低内存时被kill的几率会低一些。

#### 在onStartCommand方法内添加如下代码：
``` java
 Notification notification = new Notification(R.drawable.ic_launcher,  
 getString(R.string.app_name), System.currentTimeMillis());  
  
 PendingIntent pendingintent = PendingIntent.getActivity(this, 0,  
 new Intent(this, AppMain.class), 0);  
 notification.setLatestEventInfo(this, "uploadservice", "请保持程序在后台运行",  
 pendingintent);  
<span style="color:#ff0000;"> startForeground(0x111, notification);</span>  
```
注意在onDestroy里还需要stopForeground(true)，运行时在下拉列表会看到自己的APP在：


> 【结论】如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart


onDestroy方法里重启service

service +broadcast  方式，就是当service走ondestory的时候，发送一个自定义的广播，当收到广播的时候，重新启动service；
``` java
<receiver android:name="com.dbjtech.acbxt.waiqin.BootReceiver" >  
    <intent-filter>  
        <action android:name="android.intent.action.BOOT_COMPLETED" />  
        <action android:name="android.intent.action.USER_PRESENT" />  
        <action android:name="com.dbjtech.waiqin.destroy" />//这个就是自定义的action  
    </intent-filter>  
</receiver>  
```
在onDestroy时：
``` java
@Override  
public void onDestroy() {  
    stopForeground(true);  
    Intent intent = new Intent("com.dbjtech.waiqin.destroy");  
    sendBroadcast(intent);  
    super.onDestroy();  
}  
``` java
在BootReceiver里
``` java
public class BootReceiver extends BroadcastReceiver {  
  
    @Override  
    public void onReceive(Context context, Intent intent) {  
        if (intent.getAction().equals("com.dbjtech.waiqin.destroy")) {  
            //TODO  
            //在这里写重新启动service的相关操作  
                startUploadService(context);  
        }  
  
    }  
  
}  
```
也可以直接在onDestroy（）里startService
``` java
@Override  
public void onDestroy() {  
  
     Intent sevice = new Intent(this, MainService.class);  
     this.startService(sevice);  
  
    super.onDestroy();  
}  
```
> 【结论】当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证~.~


Application加上Persistent属性

看Android的文档知道，当进程长期不活动，或系统需要资源时，会自动清理门户，杀死一些Service，和不可见的Activity等所在的进程。但是如果某个进程不想被杀死（如数据缓存进程，或状态监控进程，或远程服务进程），可以这么做：
```
<application  
    android:name="com.test.Application"  
    android:allowBackup="true"  
    android:icon="@drawable/ic_launcher"  
    android:label="@string/app_name"  
   <span style="color:#ff0000;"> android:persistent="true"</span>  
    android:theme="@style/AppTheme" >  
</application>  
```
> 【结论】据说这个属性不能乱设置，不过设置后，的确发现优先级提高不少，或许是相当于系统级的进程，但是还是无法保证存活


监听系统广播判断Service状态

通过系统的一些广播，比如：手机重启、界面唤醒、应用状态改变等等监听并捕获到，然后判断我们的Service是否还存活，别忘记加权限啊。
``` java
<receiver android:name="com.dbjtech.acbxt.waiqin.BootReceiver" >  
    <intent-filter>  
        <action android:name="android.intent.action.BOOT_COMPLETED" />  
        <action android:name="android.intent.action.USER_PRESENT" />  
        <action android:name="android.intent.action.PACKAGE_RESTARTED" />  
        <action android:name="com.dbjtech.waiqin.destroy" />  
    </intent-filter>  
</receiver>  
```
BroadcastReceiver中：
``` java
@Override  
public void onReceive(Context context, Intent intent) {  
    if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction())) {  
        System.out.println("手机开机了....");  
        startUploadService(context);  
    }  
    if (Intent.ACTION_USER_PRESENT.equals(intent.getAction())) {  
            startUploadService(context);  
    }  
}  
```
> 【结论】这也能算是一种措施，不过感觉监听多了会导致Service很混乱，带来诸多不便

将APK安装到/system/app，变身系统级应用

这个办法不推荐使用，因为如果你的APP如果是给用户使用的，那就不合适了，我是为了给测试的妹子来用，这个APP的目的也是很简单，打开后开启Service并且能保证一直在后台驻留，开机自启动。但是昨天发现如果她的HuaWei手机长时间关闭， 再重新打开时，我们应用的Service不会自启动，貌似广播收不到了~一怒之下，打算搞成系统应用。

<!--more-->

前提：
ROOT过的手机
1. 把代码编写好后，打包导出apk，copy到手机SD卡根目录下。
2. 手机连接eclipse，cmd： adb shell
3. 切换root模式，输入：su     （如果root过就不会有错误）
4. 设置System为读写权限：mount –o remount rw /system （System默认为只读，无法写入，这一步很关键）

5. cd到sd卡跟目录下，确认是否有我们拷贝到sd卡根目录下的apk（一般都是 storage/sdcard0）
shell@android:/ # cd storage/sdcard0
6. 最关键的一步，我们要把apk拷贝到 /System/app中：

发现copy命令无效~那么我们就用push把：

如果有错误：device not found，那么手机下载一个Root Explorer把，找到apk，copy到System/app下，通过这个APP要更容易一些。
7. system/app 确定拥有我们的APK后，重启手机把：

