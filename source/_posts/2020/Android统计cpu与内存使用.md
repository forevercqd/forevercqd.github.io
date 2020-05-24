---
title: Android统计cpu与内存使用
catalog: true
date: 2020-05-22 15:00:18
subtitle:
header-img:
tags:
- Android
---


# Android获取cpu与内存性能方法
## 获取数据
```
adb shell top -m 5 > 123.list 
```

## 筛选数据到xlsx 表格
```
cat 123.list |grep "com.xxxx.rtcdemo" 123.list | awk '{print $NF"\t"$5"\t"$9}' | sed 's/K//' >123.csv
```
注意上面的$5 表示的是第5列，不同的手机可能cpu具体在哪一列有差异，可以根据实际的列号来修改具体的值;

## csv文件转excel
使用 excel 新建空白文档，然后: 开始菜单 --> 导入 --> csv;
