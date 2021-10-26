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

# 视频格式转换

比如一个视频的编码是MPEG4，想用H264编码，咋办？ 
`ffmpeg -i input.mp4 -vcodec h264 output.mp4`
相反也一样 
`ffmpeg -i input.mp4 -vcodec mpeg4 output.mp4`
 -vcodec 指定编码类型  -crf  采用cf的编码方式并设定level为23
 CRF是264和265中默认的质量/码率控制设置。这个值可以在0到51之间，值越低，质量越好，文件大小越大。在x264上面，一般取值为18-28，默认值是23

当然了，如果ffmpeg当时编译时，添加了外部的x265或者X264，那也可以用外部的编码器来编码。（不知道什么是X265，可以 Google一下，简单的说，就是她不包含在ffmpeg的源码里，是独立的一个开源代码，用于编码HEVC，ffmpeg编码时可以调用它。当然 了，ffmpeg自己也有编码器） 

```shell 
ffmpeg -i input.mp4 -c:v libx265 output.mp4 
ffmpeg -i input.mp4 -c:v libx264 output.mp4
```

 
# 视频缩放
 
```shell
./ffmpeg -i input.mp4 -vf scale=960:540 output.mp4 
```
//ps: 如果540不写，写成-1，即scale=960:-1, 那也是可以的，ffmpeg会通知缩放滤镜在输出时保持原始的宽高比。

# 关键帧提取
关键帧，是指动画中一个绘图，定义任何的起点和终点平滑过渡，一系列关键帧定义了观看者将看到的运动，而关键帧在电影，视频或动画上的位置定义了运动的时间。

获取方法，参考[视频关键帧提取](https://blog.csdn.net/qingyuanluofeng/article/details/45375647)，

`ffmpeg -i video_name.mp4 -vf select='eq(pict_type\,I)' -vsync 2 -s 1920*1080 -f image2 core-%02d.jpeg`

各个参数解释: 
* -i :输入文件，这里的话其实就是视频, 
* -vf:是一个命令行，表示过滤图形的描述, 选择过滤器select会选择帧进行输出：包括过滤器常量 
* pict_type和对应的类型:PICT_TYPE_I 表示是I帧，即关键帧。 
* -vsync 2:阻止每个关键帧产生多余的拷贝 
* -f image2 core-%02d.jpeg:将视频帧写入到图片中，样式的格式一般是: * “%d” 或者 “%0Nd” 
* -s:分辨率，1920*1080

这样保存下来的关键帧的命名顺序是从1开始的，数字表示第几个关键帧。需要保存关键帧在原始视频中的帧的位置，参考[Extracting the index of key frames from a video using ffmpeg](https://superuser.com/questions/885452/extracting-the-index-of-key-frames-from-a-video-using-ffmpeg),

`ffprobe -select_streams v -show_frames -show_entries frame=pict_type -of csv bbb480.avi | grep -n I | cut -d ':' -f 1 > frame_indices.txt
1`
会生成一个 frame_indices.txt 的文件，其中保存的即为关键帧在视频中的帧的索引位置。 
再将生成的关键帧与索引对应起来：

`ls -1 core*.jpeg >core.txt
paste core.txt frame_indices.txt > combine.txt`

生成的 combine.txt中每一行即为{}\t{}.format(core1, frame1)。
 
# 去水印
`ffmpeg -i input.mp4 -filter_complex "delogo=x=76:y=67:w=275:h=104:show=0" output.mp4` 

x,y:水印左上角的坐标
w:水印宽
h:水印高
show:0不显示绿框，1显示绿框

# 下载m3u8
 
 `ffmpeg -i https://host/really.m3u8 -c copy your.mp4`
 
# 参考文档
[常用命令](https://www.cnblogs.com/xuan52rock/p/7929509.html)