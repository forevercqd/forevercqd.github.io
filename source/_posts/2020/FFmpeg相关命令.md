---
title: FFmpeg相关命令
catalog: true
date: 2020-05-22 15:43:42
subtitle:
header-img:
tags:
- FFmpeg
---

# FFmpeg相关命令

## 音频采集转换
44100 pcm 转　48000　文件;
```
ffmpeg -y -f s16le -ac 1 -ar 44100 -acodec pcm_s16le -i test.pcm -ar 48000 -f s16le out.pcm
```

## 播放yuv
```
ffplay -pix_fmt nv12 -video_size 480x640 VTDecompressionOutputCallback_281_480x640.yuv
```

## 安装 ffmpeg, ffprobe, ffplay.
brew install ffmpeg --with-ffplay

## 查看导出视频文件的 profile,导出分辨率, fps, 导出文件时长;
ffprobe -i 输入文件名

## 查看视频帧数:
ffprobe -v error -count_frames -select_streams v:0 \
-show_entries stream=nb_read_frames -of default=nokey=1:noprint_wrappers=1  输入文件

各参数意义:
-v error这隐藏了“info”输出(版本信息等)，使解析更容易。
-count_frames计算每个流的帧数，并在相应的流部分中报告。
-select_streams v：0仅选择视频流。
-show_entries stream = nb_read_frames只显示读取的帧数。
-of default = nokey = 1：noprint_wrappers = 1将输出格式(也称为“writer”)设置为默认值，不打印每个字段的键(nokey = 1)，不打印节头和页脚(noprint_wrappers = 1)。


## GOP 长度:
ffprobe -i 输入文件 -show_frames -select_streams v | grep key_frame  > key_frame.txt
然后查看 key_frame.txt 中紧邻的两次 key_frame=1 的行号的差值即是 GOP 长度;

以上所有参数，首先验证是否与设置一致，存在问题时，先测试检查是否是测试设置问题导致。
不是测试设置原因导致，则迅速反馈给短视频开发，短视频开发修改后，再开展后续测试。



## ffmpeg 仅查看视频帧
ffprobe -i output_compose_video.mp4  -show_frames -select_streams v  

## 强制指定B帧的数量
 ./ffmpeg_g -i test_0BFrame.mp4 -vcodec h264  -g 6 -bf 3  -x264-params b-adapt=false  output.mp4
 此处关键是
 -bf 3 :指定B帧数量
 -x264-params b-adapt=false : 不使用自适应B帧

 