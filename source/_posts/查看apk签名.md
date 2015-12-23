title: 查看apk签名
date: 2014-11-20 12:36:47
tags: Android
description: 查看apk使用的签名key
---
## 看到已经签名的应用的MD5 SHA1等
```
keytool -printcert -file C:\Users\cyq\Desktop\CERT.RSA
```



所有者: CN=zzqq, OU=sina, O=coder, L=be, ST=beijing, C=86
发布者: CN=zzqq, OU=sina, O=coder, L=be, ST=beijing, C=86
序列号: 515451dd
有效期开始日期: Thu Mar 28 22:21:17 CST 2013, 截止日期: Mon Aug 13 22:21:17 CST
2040
证书指纹:
         MD5: C7:56:F5:46:0A:C7:XX:XX:XX:62:C5:EA:19:45:78:89
         SHA1: 48:07:CE:F2:31:8E:BD:XX:XX:XX:6E:2E:03:AF:62:FE:83:04:70:D8
         SHA256: 70:9B:DE:A7:37:9D:2C:0E:48:E5:21:6D:65:53:54:B4:14:8C:49:D2:B9:
6E:CA:E5:B2:1E:2E:DE:26:EF:AF:49
         签名算法名称: SHA1withRSA
         版本: 3


<!--more-->