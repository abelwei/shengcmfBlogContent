---
title: "腾讯云安装docker和docker-compose"
date: 2023-06-01T14:53:31+08:00
draft: false
categories: ["网络通讯"]
tags: ["docker", "docker-compose", "CentOS7"]
---

参考[Building the Docker](https://www.tencentcloud.com/document/product/213/37516)，腾讯云CentOS7上安装docker。
### 更新yum
```
yum update
yum install epel-release -y
yum clean all
yum list
```
---
### docker安装启用
安装:
```
yum install docker-io -y
```
启动：
```
systemctl start docker
```
设置随机启动：
```
systemctl enable docker
```
重启：
```
systemctl restart docker
```
### docker-compose安装启用
到这个[地址列表](https://github.com/docker/compose/releases?after=1.27.0-rc2)下载，找到这个版本【[v2.18.0](https://github.com/docker/compose/releases/download/v2.18.0/docker-compose-linux-x86_64)】。
>众所周知的原因，如果在服务器上不好下载，在本机下载后上传也得。

将文件重命名：
```
mv docker-compose-Linux-x86_64 /usr/local/bin/docker-compose
```
将文件移动到环境执行目录（即用直接使用docker-compose命令）：
```
mv docker-compose /usr/bin/docker-compose
```
给docker-compose可执行权限：
```
chmod +x /usr/local/bin/docker-compose
```
看看可不可以用：
```
docker-compose --version
```
