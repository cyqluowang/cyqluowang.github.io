title: gradle插件
date: 2016-08-01 08:49:26
tags: Android
description: gradle插件编写
---

##  插件编写的3总方式
1、直接在gradle文件里面编写，插件逻辑简单。
2、新建一个项目，但是不用发布到市场。
3、新建一个发布出去的插件。

## 不发布到市场的写法
1、新建醒目，名字只能是buildSrc
2、新建文件夹（buildSrc/src/main/groovy），在此文件夹下写groovy代码。
3、写入配置文件
```
apply plugin: 'groovy'
dependencies {    
   compile gradleApi()    
   compile localGroovy()  
   compile 'com.android.tools.build:gradle:2.1.2'
}
repositories {   
   mavenCentral()
}
```
4、使用  apply plugin: com.cyq.plugin.MyPlugin  
>注意没有''包裹

### 这种模式的好处是调试简单，方便，如果插件不用发布推荐这种方式

## 需要发布的写法
1、在不用发布的基础上。增加/src/main/resources/META-INF/gradle-plugins目录   
新建properties文件  我以自己的包名为开头com.xx.plugin.properties
内容：
```
implementation-class=com.cyq.plugin.MyPlugin
```
包名+类名

2、加上发布的配置
```
apply plugin: 'maven'
group='com.tt'
version='0.0.1'
uploadArchives {   
   repositories {       
   mavenDeployer {         
      repository(url: uri('../repo')) //发布到本地目录，调试      
   }  
  }
}
```
3、引用自己的插件
```
buildscript { 
    repositories { 
    maven { 
      url uri('repo')
     } 
    } 
   dependencies { 
    classpath 'com.tt.plugin:plugin:2.0.0' 
  }
}
```


## 插件代码解析
1、编写插件，继承Plugin
```
class MyPlugin implements Plugin<Project> 
```
2、读取变量
```
project.extensions.create('qiniu', APK);
ACCESS_KEY = project['qiniu'].ACCESS_KEY;
```
3、新建任务
```
 project.task(publishApkTaskName,type : UpLoadGradleTask)
```
4、整理任务的归类
```
c.group = 'qiniu'
```
5、编写依赖
```
c.dependsOn("publishApk${variationName}")
```
6、真正的任务类
```
class UpLoadGradleTask extends DefaultTask
@OptionalString key = "";接收可选参数
@TaskActiondef hello()throws IOException真正任务执行
apkOutput = variant.outputs.find { variantOutput -> variantOutput instanceof ApkVariantOutput }编译输出路径
```


## 参考
1、https://github.com/Triple-T/gradle-play-publisher
2、http://www.jianshu.com/p/8b5d9c98c2ab