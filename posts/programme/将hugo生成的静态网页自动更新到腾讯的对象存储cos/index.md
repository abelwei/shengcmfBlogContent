---
title: "将hugo生成的静态网页自动更新到腾讯的对象存储cos"
date: 2022-07-22T11:19:40+08:00
draft: false
categories: ["编程算法"]
tags: ["hugo", "cos", "golang"]
---

### 申请cos并建立子帐号授权api

1. 首先需要申请腾讯的对象存储cos,并且开通静态网页功能. [对象存储 设置静态网站-控制台指南-文档中心-腾讯云](https://cloud.tencent.com/document/product/436/14984)
2. 自动文件更新的话就需要到cos的sdk. golang的对象存储sdk: [cos-go-sdk-v5](https://github.com/tencentyun/cos-go-sdk-v5)
3. 申请API子帐户：个人帐户列表-访问管理-用户列表-新建用户。或者直接访问[这个链接](https://console.cloud.tencent.com/cam)（需要登录）
4. 寻找对应的授权（可以在搜索框上搜索“cos”），我选择的是【QcloudCOSFullAccess】。创建完成后就可以复制SecretId和SecretKey到cos的sdk中调试了。

---

### go-sdk方式更新文件到cos

用以下程序测试更新文件:

```go
package main

import (
    "context"
    "fmt"
    "net/url"
    "strings"

    "net/http"

    "github.com/tencentyun/cos-go-sdk-v5"
    "github.com/tencentyun/cos-go-sdk-v5/debug"
)

func log_status(err error) {
    if err == nil {
        return
    }
    if cos.IsNotFoundError(err) {
        // WARN
        fmt.Println("WARN: Resource is not existed")
    } else if e, ok := cos.IsCOSError(err); ok {
        fmt.Printf("ERROR: Code: %v\n", e.Code)
        fmt.Printf("ERROR: Message: %v\n", e.Message)
        fmt.Printf("ERROR: Resource: %v\n", e.Resource)
        fmt.Printf("ERROR: RequestId: %v\n", e.RequestID)
        // ERROR
    } else {
        fmt.Printf("ERROR: %v\n", err)
        // ERROR
    }
}

func main() {
    SecretID := "xxxxx"
    SecretKey := "xxxxxxxx"
    // 存储桶名称，由bucketname-appid 组成，appid必须填入，可以在COS控制台查看存储桶名称。 https://console.cloud.tencent.com/cos5/bucket
    // 替换为用户的 region，存储桶region可以在COS控制台“存储桶概览”查看 https://console.cloud.tencent.com/ ，关于地域的详情见 https://cloud.tencent.com/document/product/436/6224 。
    u, _ := url.Parse("https://test-1259654469.cos.ap-guangzhou.myqcloud.com")
    b := &cos.BaseURL{BucketURL: u}
    c := cos.NewClient(b, &http.Client{
        Transport: &cos.AuthorizationTransport{
            // 通过环境变量获取密钥
            // 环境变量 COS_SECRETID 表示用户的 SecretId，登录访问管理控制台查看密钥，https://console.cloud.tencent.com/cam/capi
            SecretID: SecretID,
            // 环境变量 COS_SECRETKEY 表示用户的 SecretKey，登录访问管理控制台查看密钥，https://console.cloud.tencent.com/cam/capi
            SecretKey: SecretKey,
            // Debug 模式，把对应 请求头部、请求内容、响应头部、响应内容 输出到标准输出
            Transport: &debug.DebugRequestTransport{
                RequestHeader: true,
                // Notice when put a large file and set need the request body, might happend out of memory error.
                RequestBody:    false,
                ResponseHeader: true,
                ResponseBody:   false,
            },
        },
    })

    // Case1 上传对象
    name := "test/example"
    f := strings.NewReader("test1111")

    _, err := c.Object.Put(context.Background(), name, f, nil)
    log_status(err)

    // Case2 使用options上传对象
    f = strings.NewReader("test xxx")
    opt := &cos.ObjectPutOptions{
        ObjectPutHeaderOptions: &cos.ObjectPutHeaderOptions{
            ContentType: "text/html",
        },
        ACLHeaderOptions: &cos.ACLHeaderOptions{
            //XCosACL: "public-read",
            XCosACL: "private",
        },
    }
    _, err = c.Object.Put(context.Background(), name, f, opt)
    log_status(err)

    // Case3 通过本地文件上传对象
    _, err = c.Object.PutFromFile(context.Background(), name, "./tests/ts1/index.html", nil)
    log_status(err)

    //// Case4 查看上传进度
    //opt.ObjectPutHeaderOptions.Listener = &cos.DefaultProgressListener{}
    //_, err = c.Object.PutFromFile(context.Background(), name, "./tests/ts1/main.go", opt)
}
```

---

### 但我想更简单的方法不用花时间写程序去更新

> 官方有提供coscli同步本地静态网页文件到cos，这样就不用这么花许多时间去做自动更新文件了。

下载地址：[coscli下载与安装配置](https://cloud.tencent.com/document/product/436/63144)
下载好后将coscli放到你喜欢的目录位置，然后用`./coscli config init`命令配置cos信息。
运行配置命令之后，会向你询问相关信息。具体参考[这里](https://cloud.tencent.com/document/product/436/63144)的**配置文件中各配置项说明**

![](Pasted%20image%2020230528230655.png)

---

使用命令同步上传文件，用命令：
`./coscli sync public/ cos://web-1111111/test/ -r`
同步上传到cos的桶上，[说明参考这里](https://cloud.tencent.com/document/product/436/63670)。
