---
title: Docker常用命令
catalog: true
date: 2020-05-22 15:25:03
subtitle:
header-img:
tags:
- Docker
---



# Docker 相关命令

## 设置 docker 镜像加速
https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors
Mac 用户为例:
需要扫码登录阿里云帐户，在"容器镜像服务 --> 镜像中心 --> 镜像加速器"，
复制加速器地址, 然后操作如下:
docker 图标，选择 Preferences ，在 Daemon 标签（Docker 17.03 之前版本为 Advanced 标签）下的 Registry mirrors 列表中将

https://55ipa7g0.mirror.aliyuncs.com加到"registry-mirrors"的数组里，点击 Apply & Restart按钮，等待Docker重启并应用配置的镜像加速器。

## 拉取镜镜像
```
docker pull ubuntu:14.04
docker images
docker run -p 5000:5000 -it -v /Users/robert/Documents:/data/Documents -e TZ=Asia/Shanghai --name 新建的窗口名 ubuntu:14.04 /bin/bash
```

## 启动镜像
```
docker start ubuntu
docker exec -it ubuntu bash
```

## 将配置好的容器制作成镜像
```
docker commit -m '注释' 容器CONTAINER_ID 镜像名称:镜像版本
```

## 保存镜像至本地
```
docker save 镜像Repository:TAG -o 输出镜像压缩包名称.tar
```

## 加载本地的镜像
```
docker load -i ubuntu_20191120.tar
```


## Docker命令详解
https://www.cnblogs.com/yfalcon/p/9044246.html
https://www.cnblogs.com/kevingrace/p/9599988.html