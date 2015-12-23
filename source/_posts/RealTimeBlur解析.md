title: RealTimeBlur解析
date: 2015-12-21 10:30:06
tags:	IOS
---
解析源码地址：https://github.com/xhzengAIB/RealTimeBlur

#主要核心功能
1.	增加一个UIView
2. 使用动画修改透明度

代码：alpha = 0.1;是关键

```oc
	[[UIView alloc] initWithFrame:self.view.bounds];  
	alpha = 0.1;
	[self.view addSubview
```

```oc
	[UIView animateWithDuration:3 delay:0 	options:UIViewAnimationOptionCurveEaseInOut animations:^{
        view.alpha = 1;
	} completion:^(BOOL finished) {
        view.alpha = 1;
	}];
```

#分析代码
1. XHRealTimeBlur做了所有该做的事情。
2. XHGradientView提供了一种蒙版的样式
3. UIView (XHRealTimeBlur)用关联的方式，在UIView中加了方式和属性，使之使用方便。
4. objc_getAssociatedObject 关联了属性，关联的主要点在这里

<!--more-->

