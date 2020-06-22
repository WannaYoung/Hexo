---
title: Flutter学习重点记录
categories:
  - 前端
tags:
  - Flutter
abbrlink: 40449
date: 2020-04-15 19:03:32
---

> Flutter 中的重点知识和报错解决记录

### 知识点

1. #### 全屏 push 实现，类似 iOS 的 present

   ```dart
   // 添加 fullscreenDialog: true
   Navigator.push(
   	context, 
   	MaterialPageRoute(
   		builder: (context) => YourPage(),
   		fullscreenDialog: true
   	)
   );
   ```

2. #### 页面跳转隐藏左上角返回按钮

   ```dart
   // 使用 Navigator.pushReplacement 替换 Navigator.push
   Navigator.pushReplacement(
   	context, 
   	MaterialPageRoute(
   		builder: (context) => YourPage(),
   	)
   );
   ```

### 错误解决

1. #### setState() called after dispose()

   界面释放以后调用 setState 方法，导致抛出异常

   ```dart
   // 调用 setState 方法前添加 !mounted 判断
   if (!mounted) return;
   setState(() {
   	//Your code
   });
   ```
   
2. #### setState() or markNeedsBuild called during build

   报错原因是手势方法调用错误，直接调用了 doSomething()

   ```dart
   // 修改为 onTap: () => doSomething()
   GestureDetector(
   	onTap: onTap: () => doSomething(), 
   	child: Text("helllo")
   )
   ```

