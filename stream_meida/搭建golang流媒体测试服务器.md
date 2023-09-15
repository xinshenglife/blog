## rtsp服务器
https://github.com/bluenviron/mediamtx  
docker-compose
```
stream_media_server:
    image: q191201771/lal
    command: "/lal/bin/lalserver -c /lal/conf/lalserver.conf.json"
    ports:
      - "1935:1935/tcp"  #rtmp
      - "8080:8080/tcp"  #http
      - "4433:4433/tcp"
      - "5544:5544/tcp"  #rtsp
      - "30000-30100:30000-30100/udp"
      - "8083:8083/tcp"  #http API  可视化界面http://127.0.0.1:8083/lal.html
      - "8084:8084/tcp"  #pprof API
```      
## rtsp推流命令--- OK
```
ffmpeg -re -stream_loop -1 -i 01.mp4 -acodec copy -vcodec copy -rtsp_transport tcp -f rtsp rtsp://localhost:5544/live/test110
```
rtsp还支持rtp over tcp的方式推流
```
ffmpeg -re -stream_loop -1 -i 02.mp4 -acodec copy -vcodec h264 -rtsp_transport tcp -f rtsp rtsp://localhost:5544/live/test264
```
## rtmp推流命令--ok
```
ffmpeg -re -i 01.mp4 -c:a copy -c:v copy -f flv rtmp://127.0.0.1/live/test110
```
## 出现问题：
1. rtsp推流出现Could not write header (incorrect codec parameters ?): Connection refused

> 解决：参数问题
