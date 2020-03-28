---
title: webrtc调试
catalog: true
date: 2020-03-29 00:05:26
subtitle:
header-img:
tags:
- webRTC
---

编译 webrtc 时添加编译参数　--args=‘is_debug=true enable_stripping=false enable_dsyms=true’　即可，如下
```
gn gen out/Debug --ide=xcode --args='is_debug=true enable_stripping=false enable_dsyms=true' --mac_deployment_target=10.10 --rtc_include_tests=true
```
