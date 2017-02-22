title: launchMode
date: 2014-11-21 10:22:09
tags: Android
---


 ## standard
 实例： 每次都会new实例 
 效果： 从新跑完activity的所有生命周期，所有视觉效果上会有一闪的效果，画面重绘了
 task： 还是在原来的栈中,不断在栈顶加入实例，不管是否存在

 ## singleTop
 实例： 如果当前实例在栈的顶端就不会new实例，否者都会new实例 
 效果： 如果在栈顶，画面不会有任何的变化，如果不是在栈顶，画面重绘，有activity替换的画面
 task： 还是在原来的栈中，如果在栈顶，栈不变，否者把新实例加入栈顶

 ## singleTask
 实例： 如果当前实例在栈的顶端就不会new实例，否者都会new实例 
 效果： 如果在栈顶，画面不会有任何的变化（不会进入任何生命周期），
 如果不是在栈顶 栈中存在，画面重绘，有activity替换的画面（不进oncreate  依次进入onNewIntent——>onResume）
 task： 还是在原来的栈中，移除在他之上的实例，使他暴露在栈顶。

 ## singleInstance
 实例： 只有一个实例，有就不会new  没有就new， 一个栈只有一个实例
 效果： 因为这里是要替换栈的，不是替换栈中实例，
 所以，画面重绘，有activity替换的画面（不进oncreate  依次进入onNewIntent——>onResume）
 task： 新实例就新栈

<!--more-->