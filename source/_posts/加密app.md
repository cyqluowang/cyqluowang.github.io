title: 加密app
date: 2014-11-24 15:26:46
tags: Android
description: 使用指定的app需要输入密码，正取才能使用
---

## 准备知识

### 获取手机上安装的应用
```
//包管理器获取到所有安装的应用
List<PackageInfo> packages = getPackageManager().getInstalledPackages(0);
for (int i = 0; i < packages.size(); i++) {
	PackageInfo packageInfo = packages.get(i);
	AppInfo tmpInfo = new AppInfo();
	tmpInfo.appName = packageInfo.applicationInfo.loadLabel(getPackageManager()).toString();
	tmpInfo.packageName = packageInfo.packageName;
	tmpInfo.versionName = packageInfo.versionName;
	tmpInfo.versionCode = packageInfo.versionCode;
	tmpInfo.appIcon = packageInfo.applicationInfo.loadIcon(getPackageManager());
	if ((packageInfo.applicationInfo.flags & ApplicationInfo.FLAG_SYSTEM) == 0) {
		appList.add(tmpInfo);// 如果非系统应用，则添加至appList
	}
}
```

### 服务启动
如果用绑定的方式启动服务的话程序退出会导致监听失败
不能锁住应用
```
startService(intentService);
```

>要保证服务一致存在，不能被销毁。
```
<service 
    android:persistent="true"
    android:name="xxx.WatchAppService"/>
```

### 服务监听
用新的线程监听前台栈中的前台进程，
如果前台进程的包名是需要加密的应用，
就启动输入，密码界面

```
//得到管理器
ActivityManager activityManager = (ActivityManager) getSystemService(Service.ACTIVITY_SERVICE);
//得到运行的线程栈，（1）表示获取一个线程栈，包含前台栈的list
//从list中得到第一个栈，最近运行的栈，get(0)
//topActivity 栈中当前正在运行的Activity
String runningpackageName = activityManager.getRunningTasks(1).get(0).topActivity.getPackageName();
```

### 密码界面
进入密码界面的时候，要设置让启动的密码界面是在一个新的栈中
因为服务不在任务栈中，密码界面会不知道放到哪个栈中
```
 intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
```

## 开机启动
最好是能开机启动，不然关机就没有了
```
public class BootBroadcastReceiver extends BroadcastReceiver {  
    //重写onReceive方法  
    @Override  
    public void onReceive(Context context, Intent intent) {  
        //后边的XXX.class就是要启动的服务  
        Intent service = new Intent(context,XXXclass);  
        context.startService(service);  
        Log.v("TAG", "开机自动服务自动启动.....");  
       //启动应用，参数为需要自动启动的应用的包名
		/**Intent intent = getPackageManager().getLaunchIntentForPackage(packageName);
		context.startActivity(intent );   **/     
    }  
  
} 

第二步：配置xml文件，在receiver接收这种添加intent-filter配置  
<receiver android:name="BootBroadcastReceiver">  
    <intent-filter>  
        <action android:name="android.intent.action.BOOT_COMPLETED"></action>  
        <category android:name="android.intent.category.LAUNCHER" />  
    </intent-filter>  
</receiver>  
第三步：添加权限 
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />  
```


## 核心代码
```
activityManager = (ActivityManager) getSystemService(Service.ACTIVITY_SERVICE);
thread = new Thread(new Runnable() {
	@Override
	public void run() {
		while (true) {
			if(((MyApplication)getApplication()).needUpdate && System.currentTimeMillis() > ((MyApplication)getApplication()).updateTime){
				try {
					needLockPackName.clear();
					for (AppInfo info : dao.queryForAll()) {
						if(info.getIsLock().equals("true")){
						  needLockPackName.add(info.getPackageName());
						}
					}
					((MyApplication)getApplication()).needUpdate = false;
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}


			String runningpackageName = activityManager.getRunningTasks(1).get(0).topActivity.getPackageName();
			//进入桌面或者进入应用管理器。
			if(runningpackageName.equals("com.android.systemui") || runningpackageName.equals("com.sec.android.app.launcher")){
				isNeedLock = true;
			}
			
			
			if(isNeedLock && needLockPackName.contains(runningpackageName)){
				isNeedLock = false;
				intentLockAppActivity.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
				intentLockAppActivity.putExtra("from", "service");
				startActivity(intentLockAppActivity);
			}
			
			
			SystemClock.sleep(2000);
		}
	
	}
});
thread.start();
```
