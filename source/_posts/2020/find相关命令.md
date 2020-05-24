---
title: find相关命令
catalog: true
date: 2020-05-24 19:35:58
subtitle:
header-img:
tags:
- Linux
---

# find相关命令

## 查找时过滤命令:
```
find / -name "build_ffmpeg.sh" 2>/dev/null | grep -v Permission
```
/dev/null 表示禁止输出， 2>/dev/null 表示所有的错误不输出。

## 对查找后结果操作
```
find ./ -name "build_ffmpeg.sh" 2>/dev/null -exec grep -v Permission {} \;
```
-exec command {} \;     // 将查到的文件执行command操作    
{}  // 表示前面命令执行的结果;   
\;  // 表示命令结束  

## 查找后结果按时间排序显示
```
find ./ -name "libQuCore.so" -exec ls -alt {} \;
```

## 递归查找内容
```
grep -ri ".build_ffmpeg" ./
```