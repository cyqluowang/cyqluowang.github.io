title: mac抓包
date: 2015-12-21 11:14:14
tags: Mac
---
在Mac中抓包，今天有用到，笔记做一下。  

1. 下载charles安装
[charles-proxy-3.9.3-applejava](/jar/charles-proxy-3.9.3-applejava.dmg)
2. 下载回来的是试用版。那我们还是替换下文件。按照教程
Contents->Resources->Java 替换charles.jar
[charles.jar](/jar/charles.jar)
3. 电脑和手机连接同一个局域网。修改手机http代理 手动
服务器：mac的ip地址
端口：8888
4. 手机打开软件，Mac中Charles确认。抓包开始

#HTTPS抓包
1. 用Safari打开，下载证书[charles-proxy-ssl-proxying-certificate.crt](/jar/charles-proxy-ssl-proxying-certificate.crt)，全部同意，安装。
2. Charles配置   
1、 在Charles的工具栏上点击Proxy按钮，选择Proxy Settings…
切换到SSL选项卡，选中Enable SSL Proxying，  
2、点击Add按钮，在弹出的表单中Host填写域名，比如填api.instagram.com，Port填443
3. 开始抓包  

#抓包结束
关闭手机http代理，不然电脑关闭charles后无网络访问。

[参考资料](http://blog.csdn.net/jiangwei0910410003/article/details/41620363)

<!--more-->