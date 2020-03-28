---
title: Camera2 新特性
catalog: true
date: 2020-03-28 23:52:27
subtitle:
header-img:
tags:
- Camera2
---


# Camera2 新特性
从 Android 5.0 开始，Google 引入了一套全新的相机框架 Camera2（android.hardware.camera2），并且废弃了旧的相机框架 Camera1(android.hardware.Camera)，其目的是配合Camera HAL3，同时赋予应用层更多更灵活的相机控制权限，从而构建出更高质量的相机应用程序。

## 更先进灵活的API架构
原Camera1中的android.hardware.Camera类被拆分成多个类，分别为CameraManager, CameraDevice, CameraCharacteristics, CameraCaptureSession, CameraRequest, ImageReader 等,　同时Camera2 API相对于Camera1 API调用
Camera2架构采用Pipeline架构，app层与Camera之前主要是通过CaptureRequest与CamerMetaData间相互通信，简化后的流程图如下:
![](camera2_simple_diagram.png)
Camera2 API架构、关键类及API接口的用法，将在下一篇文章《Camera2 API架构及API接口调用流程》中具体介绍。

## 更出色的对焦效果
Camera2在对焦精度及速度方面相对于Camera1，有了较大提升。当然因为不同的芯片厂商与手机厂商对Camera2的支持程度不同，Camer2与Camera1两者的差异大小也会因为不同的芯片及手机厂商略有不同。
以下是


## 不开启相机的条件获取相机参数
Camera1需要在打开相机后获取到具体的　android.hardware.Camera的实例后，才能进一步通过该相机实例获取到具体的相机参数(如前后置相机的CameraID, 特定相机的支持的预览分辨率列表及拍照支持的分辨辨，是否支持闪光灯等)。
Camera2可以在不打开具体相机的条件下，获取到指定相机的相机参数。
因为打开相机会出现较严重的不良用户感受甚至需要特定条件，如以下类型的手机:

## 使用弹出镜头的手机(vivo APEX, OnePlus 7Pro, oppo Reno 10倍变焦、华为P Smart Z)
可以想象在用户打开相机意愿的条件下，app层为了获取相机参数而将用户手机的镜头莫名其妙的弹出会，将会是怎么样的一种场景。
部份滑动后才能打开相机的手机(华为 mage2)
用户在不通过物理方式手动滑出摄像头时，app层是无法打开相机并获取到相机参数。
获取相机参数其实很多场景都会用到，比如上传相机的埋点信息，比如提前手机是否支持广角，进而在app启动时即可自适应显示特定功能等。
不开启预览的情况下拍照
Camera1拍照前必须先开启预览，即调用android.hardware.Camera.startPreview，该操作一方面耗时，另一方面在某些不可描述场景下显得有点尴尬。
Camera2可以在悄无声息的条件下获取拍照结果，脑补下场景，有无限的空间。

## 获取并打开更多的摄像头的权限
Camera1 的API接口Camera.getNumberOfCameras()在多摄头的手机上，仍只能获取到最多2个摄像头(1个前置、1个后置)，即使APP层明知该设备有多个摄像头可使用，并强行调用Camera.open来打开其它摄像头，会出现打开失败的结果，即最终无法充分使用设备的硬件功能。
目前多摄手机已经成为行业主流标配的环境中，Camera2获取多摄镜头并打开更多特色的镜头，已经成为Camera相关API必不可少的功能。
如广角镜头，使用Camera1无法获取并打开，Camera2可完美解决该问题。

## 获取更高的预览分辨率及拍照分辨率
相机预览更加高清的画面及拍摄更加高清的图片，已经成为手机厂商及部份app的重要卖点之一，如2019年08月爆出的新闻“全球首款 1 亿像素的传感器来了，首发厂商是小米”，讲的是三星在8月份发布了全球首款「亿」级相机传感器 ISOCELL Bright HMX， 该相机传感器最大可输出12032 x 9024 分辨率的照片。
但是目前Camera1在相机预览分辨率及拍照分辨率两项，经测试明显均劣于Camera2。

## 获取同一时刻的图片至多路输出
Camera1最多可以吐出同一时刻的相机输出流至两路输出。

一路是通过设置给相机的SurfaceTexture的回调函数onFrameAvailable，该回调函数返回的是GPU上的surface;
一路是通过设置给相机的PreviewCallback回调函数onPreviewFrame，该回调函数返回的是CPU上的裸数据;
Camera2可以吐出同一时刻的相机输出流至两路及更多路的输出。
如预览时可以输出多个surface，可以考虑一个surface做人脸特征点检测，一个surface做渲染，一个surface给硬件编码器。

## 获取同一时刻不同的分辨率的相机输出流
Camera1输出的两路流的分辨率无法配置成不同的分辨率，但是Camera2可以配置成不同的输出分辨率，如预览时可使用更高的分辨率1080p或2K来提升预览清晰度，但是送给人脸相关的检测算法的图像的分辨率却可以使用更小的分辨率(如720p，480p等)，在既提升预览清晰度的同时，又降低人脸相关算法的耗时。

## 获取多张不同格式和尺寸的拍照图片
Camera1一次拍照只能获取一张指定格式指定分辨的图片，但是Camera2可以一次获取多张图片，且各图片格式可以不一样，分辨率也可以不一样，为用户更多复杂的场景提供了灵活性。

连拍功能
Camera1 API没有提供连拍功能，用户要实现连拍功能，只能在一次拍照结束并返回数据后，再循环调用拍照API接口来实现连拍功能，因此必须串行执行。另外Camera1从拍照命令执行至返回拍照结果间的耗时较长，不同手机，不同的图片分辨率均对耗时产生重大影响。
Camera2实现连拍可以直接调用captureBurst接口实现连拍功能，该实现过程一次性向CameraDevice发送了多次拍照请求，系统中可流水线式依次输出连拍的图片，效率较Camera1更高，使用更快捷方便。

控制曝光时间
Camera1无法控制曝光时间，Camera2可灵活设置曝光时间，在暗光条件下，设置曝光时间可较大程度改善照片清晰度。

支持帧级别的相机参数调整
Camera2每一帧数据返回

更灵活的线程控制
开启摄像头线程、预览线程、拍照回调线程都均可自已控制。
