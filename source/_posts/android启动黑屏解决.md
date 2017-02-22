title: android启动黑屏解决
date: 2014-11-03 17:39:19
tags: Android
description: 使用Android主题解决启动黑屏
---


## 1、自定义Theme


### 1、设置背景图Theme
``` java
<style name="Theme.AppStartLoad" parent="android:Theme">  
    <item name="android:windowBackground">@drawable/ipod_bg</item>  
    <item name="android:windowNoTitle">true</item>  
</style>
```

### 2、设置透明Theme
``` java
<style name="Theme.AppStartLoadTranslucent" parent="android:Theme">  
    <item name="android:windowIsTranslucent">true</item> 
    <item name="android:windowNoTitle">true</item>  
</style>
```

> 上面我定义了两种Theme，
第一种Theme就是设置一张背景图。当程序启动时，首先显示这张背景图，避免出现黑屏。
第二种Theme是把样式设置为透明，程序启动后不会黑屏而是整个透明了，等到界面初始化完才一次性显示出来。
实际上的效果是点了看去好像没有启动，
<!--more-->

> 下面说说两种方式的优缺点：
Theme1 程序启动快，界面先显示背景图，然后再刷新其他界面控件。给人刷新不同步感觉。
Theme2 给人程序启动慢感觉，界面一次性刷出来，刷新同步。
 

## 2、修改AndroidManifest.xml

为了使上面Theme生效，我们需要设置一些Activity的Theme

``` java
<application
    android:allowBackup="true"
    android:icon="@drawable/ipod_icon"
    android:label="@string/app_name"
    android:launchMode="singleTask">

	<activity
	    android:name="com.apical.apicalipod.IPodMainActivity"
	　　<!-- 使用上面定义的样式 mythou-->
	    android:theme="@style/Theme.AppStartLoad"
	    android:label="@string/app_name" >
	    <intent-filter>
	    <action android:name="android.intent.action.MAIN" />
	    <category android:name="android.intent.category.LAUNCHER" />
	    </intent-filter>
	</activity>
</application>
```
可以在Activity里面增加上面自定义的样式。另外在Application里面增加也是可以的，而且是全局效果。
自定义Theme放在 /res/values/styles.xml 里面。如果没有这个文件，自己添加一个即可。
如果存在多个Activity切换，中间也可能会存在短暂黑屏问题。原因也是Activity启动的时候需要初始化加载数据，如果想避免这种情况，可以在你切换的Activity里面增加上面的样式。
上面两种样式都可以避免黑屏。可以实际测试一下你的程序选择一种效果。
这个只是把黑屏避免了，但是如果你程序初始化启动慢，还是会给人程序启动慢的感觉。需要自行优化程序初始化过程。
## 3、Theme属性详解

android:theme="@android:style/Theme.Dialog" //Activity显示为对话框模式

android:theme="@android:style/Theme.NoTitleBar" //不显示应用程序标题栏

android:theme="@android:style/Theme.NoTitleBar.Fullscreen" //不显示应用程序标题栏，并全屏

android:theme="Theme.Light " //背景为白色

android:theme="Theme.Light.NoTitleBar" //白色背景并无标题栏

android:theme="Theme.Light.NoTitleBar.Fullscreen" //白色背景，无标题栏，全屏

android:theme="Theme.Black" //背景黑色

android:theme="Theme.Black.NoTitleBar" //黑色背景并无标题栏

android:theme="Theme.Black.NoTitleBar.Fullscreen" //黑色背景，无标题栏，全屏

android:theme="Theme.Wallpaper" //用系统桌面为应用程序背景

android:theme="Theme.Wallpaper.NoTitleBar" //用系统桌面为应用程序背景，且无标题栏

android:theme="Theme.Wallpaper.NoTitleBar.Fullscreen" //用系统桌面为应用程序背景，无标题栏，全屏

android:theme="Theme.Translucent" //透明背景

android:theme="Theme.Translucent.NoTitleBar" //透明背景并无标题

android:theme="Theme.Translucent.NoTitleBar.Fullscreen" //透明背景并无标题，全屏

android:theme="Theme.Panel " //面板风格显示

android:theme="Theme.Light.Panel" //平板风格显示
 

## 4、Theme和Style

Android里面除了Theme外还有Style，例如下面是Launcher里面配置workspace的一个Style
``` java 
 　<style name="WorkspaceIcon">
        <item name="android:layout_width">match_parent</item>
        <item name="android:layout_height">match_parent</item>
        <item name="android:layout_gravity">center</item>
        <item name="android:gravity">center_horizontal</item>
        <item name="android:singleLine">true</item>
        <item name="android:ellipsize">marquee</item>
        <item name="android:textSize">12sp</item>
        <item name="android:textColor">#FFF</item>
        <item name="android:shadowRadius">2.0</item>
        <item name="android:shadowColor">#B0000000</item>
    </style>
```
Style可以理解为一组属性集合，方便不同的View设置使用，我们在View里面使用Style的时候，跟使用Theme是一样的应用方法。那么Style和Theme有什么区别？下面列出两者区别：

> 样式用在单独的View，如：Button、TextView等
主题通过AndroidManifest.xml中的<application>和<activity>用在整个应用或者某个 Activity，主题对整个应用或某个Activity存在全局性影响。
如果一个应用使用了主题，同时应用下的view也使用了样式，那么当主题与样式属性发生冲突时，样式的优先级高于主题。
上面就是通过Theme解决程序启动闪黑屏问题，并且讲解了Theme和Style，
通过Theme配置，其实还可以做个欢迎页面。不过我们都希望程序启动速度越快越好，因此还是需要多多优化自己的程序。
