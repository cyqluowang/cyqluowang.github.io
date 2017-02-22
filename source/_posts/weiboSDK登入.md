title: weiboSDK登入
date: 2014-11-20 12:25:23
tags: Android
description: weiboSDK使用
---
## 注册应用
获取到APP_KEY，填好回掉地址，SCOPE是应用的权限

## 下载sdk
以library方式加入项目

## 代码
sso登入的话主要拉出来demo 中WBAuthActivity，修改好自己的参数，
加上必要的工具类即可。

## 打包

## 获取MD5签名
安装好sdk中自带的签名工具包，app_signature.apk
获取签名，

## 去开放平开添加签名
找到自己的应用，编辑加入Android包名（package名）
Android签名（获取到的md5值）

> 这里一定要在安装好应用后再去修改，不同的包签名是不一样的，如果MD5值不对就会报错
 sso package or sign error



<!--more-->