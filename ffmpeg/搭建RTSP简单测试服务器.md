## rtsp服务器
https://github.com/bluenviron/mediamtx
## rtsp播放器
Mac RTMP RTSP测试播放器https://iina.io/ 现在直接用 IINA 就行了，基于 mpv，简单好用。
或者直接二进制下载

## rtsp推流命令
```
ffmpeg -re -stream_loop -1 -i 01.mp4 -preset ultrafast -vf scale=iw/2:ih/2 -strict -2 -c:a copy -f rtsp rtsp://127.0.0.1:8554/live/test1  
```
-vf 和 -c:v copy 不能同时使用否则冲突
```
ffmpeg -re -stream_loop -1 -i 02.mp4 -vf scale=320:240 -c:a copy -c:v h264 -f rtsp rtsp://127.0.0.1:8554/live/test2 --无效了
```
- -stream_loop  -1 表示无限循环 
- -re 按照原视频帧率
- 只能参数后面空格一个 不然多个空格会导致无法识别ffmpeg6.0
```
ffmpeg -i video_320x180.mp4 -vf scale=320:240,setdar=4:3 video_320x240.mp4 -c:a copy -c:v h264 -hide_banner //设置分辨率小 指定视频编码
```
- -vf scale 和-s效果一样 设置分辨率  setdar 设置 宽高比例 16:9  4:3参考https://blog.csdn.net/ternence_hsu/article/details/108810527  sacle 宽高最大的 然后另外一个会自动成比例缩小
- -strict -2  表示aac音频编码 表示使用自带的 否则其他的需要第三方解码库
- -b:v  指定码率 单位比特/秒（bps     和带宽有关。本地测试应该带宽不是问题。 那就是需要更高分辨率

都是RTSP流 转发推流到其他服务器
```
ffmpeg -fflags +genpts -rtsp_transport tcp -i rtsp://192.168.50.15:8554/live/test1 -c copy -preset ultrafast -f flv rtmp://192.168.50.15/live/test1
```
- -flvflags no_duration_filesize 这个参数是关键，这个参数告诉ffmpeg不要抛出duration_filesize警告  不过最佳是 -stream_loop -1   无限循环就不会报错了
- -rtsp_transport tcp 强制读取rtsp 使用tcp
- -preset 用编码时间来换视频质量的参数
- -r 20 还可以降低帧率来保障速度
- -fflags +genpts  参考https://www.jianshu.com/p/185f365e031a  增加直播平缓 pts has no value  帧时间戳不一致相关的问题
‘genpts’Generate missing PTS if DTS is present.
fflags flags
Set format flags.
Some are implemented for a limited number of formats.
## 出现问题：
1. ffmpeg broken pipe
增加缓冲区：可以尝试增加管道缓冲区的大小，以便程序可以更好地处理视频。这可以通过增加管道的缓冲区大小来实现，例如通过添加"-bufsize"参数来设置缓冲区的大小
https://juejin.cn/s/python%20ffmpeg%20broken%20pipe

2. 同一个系统内  推流 服务--拉流 --推流 会导致  推流端broken pipe  拉流端放在其他系统内就不会导致推流端broken----还是一样的会导致推流端borken 长时间没有响应
3. Could not find codec parameters for stream 0 (Video: h264, none): unspecified size
Consider increasing the value for the 'analyzeduration' (0) and 'probesize' (5000000) options
 强制ffmpeg 以tcp传输 ，可以解决上面返回 none的情况  https://www.cnblogs.com/wainiwann/p/7301534.html
ffmpeg强制使用TCP方式读取rtsp流 ffmpeg -rtsp_transport tcp -i rtsp://a
4.  co located POCs unavailable /rtmp播放 服务器显示invalid POC
Failed to update header with correct duration
Failed to update header with correct filesize   -stream_loop -1   无限循环就不会报错了------大多数情况下都是没有后续数据 下载不到数据。导致直播流中断
推流没有问题。但是无法播放。并且服务器显示invalid POC  -----最后使用自己的rtmp服务器
但是报错 参考https://mlog.club/article/4269478
h264 @ 0xffff87435b80] co located POCs unavailable  无法定为 缺少关键帧
[h264 @ 0xffff87435b80] mmco: unref short failure 未刷新失败
Non-monotonous DTS--解决办法 降低帧率  -r 20  减少分辨率
-preset的所有的预设按照编码速度降序排列为：
ultrafast,superfast,veryfast,faster,fast,medium,slow,slower,veryslow,placebo默认为medium。

5. av_interleaved_write_frame(): End of file  解决了上述降低画质 提高传输速度和流畅度。但是出现这个
设置-r 帧率低了 服务器会主动断掉
参考https://blog.csdn.net/CrystalShaw/article/details/122917522
网络质量太差主动断掉了  或者还是出现 co located POCs unavailable
this may result in incorrect timestamps in the output file  降低帧率导致这个 原因是不连贯 采样异常https://zhuanlan.zhihu.com/p/342911090---视频太大 带不动  换小视频推流 就暂时没有问题

6. Codec mpeg4 is not supported in the official FLV specification  use vstrict=-1 / -strict -1 to use it anyway 在成比例降低分辨率后或者降低码率后
拉流转发推流的时候 出现的  然后按照提示加上 -strict -1 解决了 但是出现Codec mpeg4 is not supported in the official FLV specification 而且也无法播放  可能原因是接受的mp4编码不是h264
经过测试拉流后保存10秒到视频 编码AAC，MPEG-4 Video  确实没有h264.  推流的时候指定下输出编码是h264 推流的时候忘记加-c copy 
7. 转码后视频 推流的时候Codec AVOption preset (Encoding preset) has not been used for any stream. The most likely reason is either wrong type  
推流的时候忘记编码加  -c copy

