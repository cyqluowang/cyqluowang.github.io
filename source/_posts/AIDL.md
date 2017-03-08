title: AIDL
date: 2017-03-08 20:45:10
tags: Android
---

# 环境
开发工具：Android studio

# 整体的文件目录

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/295077-1b9fafc37949a3fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# server端编写
1、在Java那边新建aidl文件，会自动生成和Java并列的aidl文件夹，包名也是一致的

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/295077-2c054bfd410ef39d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

会自动生成代码
```
interface IMyAidlInterface {
    /**
     * Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
}
```
不用管，加上自己的测试代码
```
interface IMyAidlInterface {
    /**
     * Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
    //测试代码
    void myTest();
}
```

2、新建service文件，主要就是返回IBinder对象
```
public class myService  extends Service{
    @Override
    public void onCreate() {
        super.onCreate();
        Log.e("myService--->","onCreate");
    }

    private IBinder iBinder= new IMyAidlInterface.Stub() {
        @Override
        public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, String aString) throws RemoteException {

        }

        @Override
        public void myTest() throws RemoteException {
            Log.e("iiiiiiiii--->","yeyeyyeyeyeyeye");
        }
    };

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return iBinder;
    }
}
```
3、注册service
```
<service android:name="com.renrun.service.myService"
                 android:enabled="true"
                 android:exported="true"
                 android:process=":remote">
            <intent-filter>
                <action android:name="com.renrun.service.myService"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>

</service>

android:enabled  当前Activity是否可以被另一个Application的组件启动：true允许被启动；false不允许被启动。
只要有filter，默认值就是true,否则是false

android:exported 其它应用的组件是否可以唤醒service或者和这个service进行交互：true可以，false不可以
只要有filter，默认值就是true,否则是false

android:process 标志在哪个线程，：开头表示是私有的，会补上包名，和com.renrun.service.remote相比，就是公有和私有的区别。

action android:name 表示可以通过这个名称来启动这个service
category android:name 是默认的模式
```

# client端编写
1、拷贝service端的aidl整个文件夹到同样的地方。
2、调用服务,初始化变量
```
  IMyAidlInterface iMyAidl;
        private ServiceConnection conn=new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            Log.d("cyqlog", "onServiceConnected success");
            iMyAidl=IMyAidlInterface.Stub.asInterface(service);  //  1

        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
            Log.d("cyqlog", "onServicedisConnected ");
            iMyAidl=null;
        }
    };
```
调用
```
 Intent intent = new Intent();
        //绑定服务端的service
        intent.setAction("com.renrun.service.myService");
//        intent.setPackage("com.renrun.service");
        //新版本（5.0后）必须显式intent启动 绑定服务
        intent.setComponent(new ComponentName("com.renrun.service","com.renrun.service.myService"));
        startService(intent);
        //绑定的时候服务端自动创建
        bindService(intent,conn, Context.BIND_AUTO_CREATE);

        Boolean b = bindService(intent,conn, Context.BIND_AUTO_CREATE);
        Log.e("i00---00-90------>","1-->"+b);
        new Handler().postDelayed(new Runnable(){
            public void run() {
                try {
                    iMyAidl.myTest();
                } catch (Exception e1) {
                    e1.printStackTrace();
                }
            }
        }, 1000);

```
## 特别注意：
按照正常的流程startService(intent);是不需要的，
但是魅族是个奇葩，没有startService(intent); bindService返回绑定失败，所以通用写法一定要加上。

# 测试运行
先运行server，再运行oo，能看到service日志打印。