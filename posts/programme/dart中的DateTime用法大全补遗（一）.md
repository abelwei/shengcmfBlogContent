---
title: "dart中的DateTime用法大全补遗（一）"
date: 2023-05-30T22:51:44+08:00
draft: false
categories: ["编程算法"]
tags: ["dart", "flutter"]
---

在时间库在任何一个编程语言里都是硬需求，官方时间库都会大而全的全方位支持可能的需求。

但就是因为大而全，才会有非常复杂的用法。我将会在实践使用过程中一个个地在使用过程中补遗。

时间戳转时间格式：

``` dart
//定义到数据格式 yyyy-MM-dd hh:mm:ss
DateFormat inputFormat = DateFormat("yyyy-MM-dd"); 
//从datetime类中的函数毫秒转到datetime对象格式
DateTime dateTime = DateTime.fromMillisecondsSinceEpoch(timestamp*1000); 
inputFormat.format(dateTime);//这里就可以输出字符串格式了
```

datetime转时间戳

``` dart
DateTime dateTime = DateTime.now();
dateTime.millisecondsSinceEpoch/1000; //秒
dateTime.millisecondsSinceEpoch; //毫秒
```

时间格式字符串转时间戳

``` dart
              var parsedDate = DateTime.parse(_birthday);
              int birthdayInt = parsedDate.millisecondsSinceEpoch~/1000;

```

