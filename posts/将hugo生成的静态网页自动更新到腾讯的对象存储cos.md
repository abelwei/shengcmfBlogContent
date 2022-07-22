---
title: "将hugo生成的静态网页自动更新到腾讯的对象存储cos"
date: 2022-07-22T11:19:40+08:00
draft: false
---

# 将hugo生成的静态网页自动更新到腾讯的对象存储cos

首先需要申请腾讯的对象存储cos,并且开通静态网页功能. [对象存储 设置静态网站-控制台指南-文档中心-腾讯云](https://cloud.tencent.com/document/product/436/14984)

自动文件更新的话就需要到cos的sdk. golang的对象存储sdk: [cos-go-sdk-v5](https://github.com/tencentyun/cos-go-sdk-v5)

使用sdk的话,需要api帐号,为了安全申请子帐号.

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
