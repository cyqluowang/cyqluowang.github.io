title: Maven
date: 2017-02-22 17:44:27
tags: spring
---
# 下载
1、[官网](http://maven.apache.org/download.cgi)
2、下载2进制压缩文件，解压到自己的工作目录(最好不要有中文)
# 配置环境变量
1、首先要配置JAVA_HOME的环境变量，没有这个会报错
```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
2、配置maven环境(更具自己的环境实际改变)
```
export M2_HOME=/Users/xx/Documents/java/apache-maven-3.3.9
export PATH=$PATH:$M2_HOME/bin
```
3、刷新环境变量，使其生效
```
 $ source ~/.bash_profile
```
4、重启终端 运行 mvn -v
```
➜  ~ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /Users/xx/Documents/java/apache-maven-3.3.9
Java version: 1.8.0_91, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.12.2", arch: "x86_64", family: "mac"
```

# 配置修改（settings.xml）
1、修改默认的本地仓库地址
```
<localRepository>自己制定的全路径目录</localRepository>
```
2、修改远程仓库地址，不然下载依赖很慢
```
 <mirror>  
        <id>repo2</id>  
        <mirrorOf>central</mirrorOf>  
        <name>Human Readable Name for this Mirror.</name>  
        <url>http://repo2.maven.org/maven2/</url>  
</mirror>
```


# 资料

* 远程仓库地址：https://my.oschina.net/fdblog/blog/546938
