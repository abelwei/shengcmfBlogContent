---
title: "flutter给聊天软件的桌面做一个退出事件"
date: 2023-06-11T22:13:53+08:00
draft: false
categories: ["编程算法"]
tags: ["flutter", "WillPopScope", ""]
---

我需要给聊天软件的桌面做一个退出事件。移动端是有`WillPopScope`组件的。桌面端我使用[window_manager第三方插件](https://pub.dev/packages/window_manager)管理桌面窗口。
搜索中文资料看得很懵逼，直接先找官方资料文档看看。
[官方资料](https://pub.dev/packages/window_manager#macos)有这一段代码：

```dart
import 'package:flutter/cupertino.dart';
import 'package:window_manager/window_manager.dart';

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with WindowListener {
  @override
  void initState() {
    super.initState();
    windowManager.addListener(this);
    _init();
  }

  @override
  void dispose() {
    windowManager.removeListener(this);
    super.dispose();
  }

  void _init() async {
    // Add this line to override the default close handler
    await windowManager.setPreventClose(true);
    setState(() {});
  }

  @override
  Widget build(BuildContext context) {
    // ...
  }

  @override
  void onWindowClose() async {
    bool _isPreventClose = await windowManager.isPreventClose();
    if (_isPreventClose) {
      showDialog(
        context: context,
        builder: (_) {
          return AlertDialog(
            title: Text('Are you sure you want to close this window?'),
            actions: [
              TextButton(
                child: Text('No'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
              TextButton(
                child: Text('Yes'),
                onPressed: () {
                  Navigator.of(context).pop();
                  await windowManager.destroy();
                },
              ),
            ],
          );
        },
      );
    }
  }
}
```

拿到我的代码之下适配一下：

1. 混入窗口继承：`... with WindowListener {`

2. 加入初始化：
   
   ```dart
   @override  
   void initState() {  
   if (isDesktop) {  
    windowManager.addListener(this);  
    _init();  
   }  
   super.initState();  
   }
   ```

```dart
Future<void> _init() async {  
 await windowManager.setPreventClose(true);  
 setState(() {});  
}
```

3. 加入despose事件:

```dart
@override  
void dispose() {  
  if (isDesktop) {  
    windowManager.removeListener(this);  
  }  
  super.dispose();  
}
```

4. 加入关闭窗口事件：
   
   ```dart
   @override  
   Future<void> onWindowClose() async {  
   
   bool isPreventClose = await windowManager.isPreventClose();  
   if (isPreventClose) {  
   print("关闭窗口事件");  
   await windowManager.destroy();  
   }  
   
   super.onWindowClose();  
   }
   ```

这样点击窗口的关闭按钮就会触发事件了。
