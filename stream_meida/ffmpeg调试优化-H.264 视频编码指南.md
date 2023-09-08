参考https://trac.ffmpeg.org/wiki/Encode/H.264
- –vcodec copy  视频转码的时候 copy 就不会发生转码，直接复用  参考https://www.cnblogs.com/monjeo/p/7605935.html
- -preset的所有的预设按照编码速度降序排列为：
ultrafast,superfast,veryfast,faster,fast,medium,slow,slower,veryslow,placebo默认为medium。
- -crf参数   CRF 比例的范围是 0-51，其中 0 是无损的（仅适用于 8 位，对于 10 位使用 -qp 0），23 是默认值，51 是可能的最差质量
- -threads 限制使用的线程数 一般不限制
18～28 18视觉无损  参考https://blog.csdn.net/happydeer/article/details/52610060
- -profile:v
- -tune 主要配合视频类型和视觉优化的参数


- -maxrate  最大比特率
- -bufsize 速率控制缓冲区
- -movflags +faststart  快速启动
- -b:v 1000k  设置视频码率 也就是每秒多少大小数据输出
修改视频分辨率--参考https://www.jianshu.com/p/4e54e99fe861  https://blog.csdn.net/yu540135101/article/details/84346505
法一：
```
ffmpeg -i 1.mp4 -strict -2 -s 640x480 4.mp4
```
缺点:如果分辨率的比例跟原视频的比例不一样，会导致视屏变形
法二：
```
ffmpeg -i 1.mp4 -strict -2 -vf scale=-1:480 4.mp4 
```
-1表示按照比例缩放，可保证视屏不会变形