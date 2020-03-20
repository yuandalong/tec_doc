# 获取视频信息

```shell
./ffmpeg -i ~/Downloads/test.mp4
```
几个关键信息:
Duration 时长
bitrate 码率
Video 视频信息，其中的 ?*?就是视频的分辨率
Audio 音频信息
# 视频压缩
```shell
./ffmpeg -i ~/Downloads/test.mp4 -b:v 1024k -bufsize 1024K -s 720*480 ~/work/tmp/test.mp4
```
参数说明：
-i 输入文件
-b:v 视频码率 注意k别漏了，默认是200k，1080p的视频用1000k就可以接受了
-bufsize 用于设置码率控制缓冲器的大小，设置的好处是，让整体的码率更趋近于希望的值，减少波动。
-s 视频分辨率
-r 设定帧速率，默认为25

## 常见视频尺寸

```
1080P=1920*1080
720p=1280*720
480p=720*480
360p=480*360
240p=320*240
```

# 视频截图

```shell
./ffmpeg -i ~/Downloads/test.mp4 -y -f image2 -ss 10 -t 0.01 -s 1920*1080 ~/work/tmp/test.jpg
```
-f 设定输出格式 截图用image2
-ss 开始时间 单位秒
-t 截取时间 截图用0.001就可以了
-y 覆盖输出文件
-s 生成图片大小

# 参考文档
[常用命令](https://www.cnblogs.com/xuan52rock/p/7929509.html)