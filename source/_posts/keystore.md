title: keystore
date: 2014-11-20 12:42:00
tags: Android
description: eclipse中的keystore
---

## Default debug keystore
这个可以是在平常的时候run时候使用的key

## Custom debug keystore
如果加了这个的话，run的时候会覆盖Default debug keystore
apk的签名就是Custom的。

![img](/img/keystore.png)

## 查看keystore信息
keytool -list  -v -keystore xxxx.keystore


## 修改keystore信息，使能够在Custom debug keystore使用

1. 首先当然是先复制一份正式证书出来作为要修改为的临时调试证书。

2. 修改keystore密码的命令(keytool为JDK带的命令行工具)：
```
keytool -storepasswd -keystore my.keystore
```
其中，my.keystore是复制出来的证书文件，执行后会提示输入证书的当前密码，和新密码以及重复新密码确认。这一步需要将密码改为android。

3. 修改keystore的alias：
```
keytool -changealias -keystore my.keystore -alias 现在的别名 -destalias androiddebugkey
```
这一步中，my_name是证书中当前的alias，-destalias指定的是要修改为的alias，这里按规矩来，改为androiddebugkey！这个命令会先后提示输入keystore的密码和当前alias的密码。

4. 修改alias的密码：
```
keytool -keypasswd -keystore my.keystore -alias androiddebugkey
```