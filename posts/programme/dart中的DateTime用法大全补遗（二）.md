---
title: "dart中的DateTime用法大全补遗（二）"
date: 2023-06-09T21:48:43+08:00
draft: true
categories: ["编程算法"]
tags: ["dart", "flutter"]
---

转DateTime的时候，字符串格式不一定正确，可以用try捕获格式异常：

```dart
String strBrithday = "2000-12-1";
DateTime dtBirthday;
try{
  dtBirthday = DateTime.parse(strBrithday);
}catch(_){ //如果有异常就将DataTime默认为2000年1月1日
  dtBirthday = DateTime(2000,1,1);
}
print(dtBirthday);
```
