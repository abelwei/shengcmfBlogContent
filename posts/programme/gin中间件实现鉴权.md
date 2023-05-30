---
title: "gin中间件实现鉴权"
date: 2023-05-30T13:15:39+08:00
draft: false
categories: ["编程算法"]
tags: ["gin", "golang", "jwt"]
---

web应用中的鉴权是硬性需求。

我用[jwt-go库](github.com/dgrijalva/jwt-go)（虽然作者停止了更新）做JSON Web Token（JWT）。怎么使用[jwt-go库](github.com/dgrijalva/jwt-go)，看这篇文章[Go 鉴权（三）：JWT](https://www.jianshu.com/p/dc0e1b7332d3)。

### 怎么用gin中间件实现鉴权拦截：

一、先写一个函数：

```go
func Auth() gin.HandlerFunc {
    return func(ginContext *gin.Context) {
        //CheckJwt是解码得到对象信息
        err, user := CheckJwt(ginContext)
        if err != nil {
            //如果有错误就中止并返回错误信息给前端，这里都是伪代码
            response.Err(ginContext, response.ErrUnknown)
            ginContext.Abort()
            return
        }
        //如果鉴权成功，给上下文context设置用户信息，在下游中可以使用这个【auth-user】对象信息
        ginContext.Set("auth-user", user)
        ginContext.Next()
    }
}
```

二、在某个路由组中实现鉴权：

```go
group := ginEngine.Group("/api/user")
group.Use(Auth()) //用中间件方式对一个路由组做鉴权
```

其实也可以对某个路由做鉴权：

```go
group := ginEngine.Group("/api/user")
group.GET("/info", GetUserInfo, Auth()) //指定某个路由做鉴权
```

### 可以在下游的对象中得到用户的鉴权信息

```go
func GetUserInfo(ginContext *gin.Context) {
  userId := ""
  user, ok := ginContext.Get("auth-user")
  if ok {
    userId = user.(User).Id //在上游的鉴权设置用户对象之后，得到用户id
  }
  ...
}



```


