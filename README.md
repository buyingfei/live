# 从零开始搭建直播平台

### 项目实现目标
1. 直播协议介绍：RTMP、HLS、HDL（HTTP-FLV）、RTP
2. 直播软件介绍及安装
3. 播放本地视频、远程视频流、rtmp 直播、hls直播
4. 采坑总结


### RTMP、HLS、HDL（HTTP-FLV）、RTP 协议介绍
* RTMP协议是Adobe的专利协议，需要浏览器安装FlashPlayer，底层基于TCP，基于RTMP的直播一般内容延迟在2~5秒
* HDL（HTTP-FLV）使用HTTP协议流式的传输媒体内容。相对于RTMP，HTTP更简单和广为人知，而且不担心被Adobe的专利绑架。内容延迟同样可以做到2~5秒，打开速度更快，因为HTTP本身没有复杂的状态交互。所以从延迟角度来看，HTTP-FLV要优于RTMP。
* HLS即Http Live Streaming，是由苹果提出基于HTTP的流媒体传输协议。HLS有一个非常大的优点：HTML5可以直接打开播放；这个意味着可以把一个直播链接通过微信等转发分享，不需要安装任何独立的APP，有浏览器即可，所以流行度很高HLS。则有5~7秒的内容延迟
* RTP协议，RTP和它们有一个重要的区别就是默认是使用UDP协议来传输数据，而RTMP和HTTP是基于TCP协议传输。选择RTP进行直播则可以做到1秒内的直播延迟。但就目前所了解，各大CDN厂商没有支持基于RTP直播的，所以目前国内主流还是RTMP或HTTP-FLV。

### 安装nginx-rtmp 拓展 [注意nginx 版本，1.3.14 - 1.5.0 需要安装http_ssl_module]
**nginx 版本不在 1.3.14 - 1.5.0 之间**

```bash
# cd to NGINX source directory & run this:
./configure --add-module=/path/to/nginx-rtmp-module
make
make install
```

**nginx 版本在 1.3.14 - 1.5.0 之间，需要引入 http_ssl_module**
```bash
# cd to NGINX source directory & run this:
./configure --add-module=/path/to/nginx-rtmp-module --with-http_ssl_module
make
make install
```

**增加nginx 调试日志**
```bash
#For building debug version of nginx add --with-debug
./configure --add-module=/path/to-nginx/rtmp-module --with-debug
make
make install
```

**检测是否安装成功**
```bash
sudo /usr/local/nginx/sbin/nginx -s reload
/usr/local/nginx/sbin/nginx -V
```
如下：
```bash
nginx version: nginx/1.12.1
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-18) (GCC)
built with OpenSSL 1.0.1e-fips 11 Feb 2013
TLS SNI support enabled
configure arguments: --add-module=/opt/app/nginx-rtmp-module-master --with-debug
```
则安装成功！



### 准备工具简介
* windows 下OBS 进行推流[下载地址：](https://obsproject.com/download?spm=a2c4g.11186623.2.3.4b821445EVKONB)
* linux 下 ffmpeg[下载地址：](https://obsproject.com/download?spm=a2c4g.11186623.2.3.4b821445EVKONB)
* VLC media play [下载地址：](https://www.videolan.org/)

### OBS简单使用
**设置场景，选择来源**
* 视频捕获设备，一般都是摄像头进行推流
* 显示器捕获，就是选择显示器进行推流
* 选择媒体源，一般就是选择本地视频文件，进行推流

### ffmpeg 进行推流
通过二进制yum 安装[仅限于cent 6.5，其他版本请自行安装]
```bash
sudo rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
sudo rpm -Uvh http://li.nux.ro/download/nux/dextop/el6/x86_64/nux-dextop-release-0-2.el6.nux.noarch.rpm
sudo yum install ffmpeg ffmpeg-devel -y
```

```bash
ffmpeg -re -i /home/buyf/live/record/2345-1536202257.flv -loop 1000 -vcodec  libx264 -vprofile baseline -acodec aac -ar 44100 -strict -2 -ac 1 -f flv -s 1280x720 -q 10 rtmp://192.168.132.128:1935/localRTMP/local
```


**VLC 进行播放视频流**
### 播放本地视频，进行点播
```bash
VLC - 媒体-打开网络串流 ,写入串流地址
rtmp://192.168.132.128:1935/localVideo/123.mp4
```


### 播放远程视频流
```bash
VLC - 媒体-打开网络串流 ,写入串流地址
rtmp://192.168.132.128:1935/remoteRTMPStream
```

### 通过rtmp 进行直播
```bash
#OBS-流-自定义媒体服务器URL
URL: rtmp://192.168.132.128:1935/localRTMP
流名称： 2345
http 访问：http://192.168.132.128:9510/web/rtmp.html
```

### 通过hls 进行直播
```bash
#OBS-流-自定义媒体服务器URL
URL: rtmp://192.168.132.128:1935/hls
流名称： 2345
http 访问：http://192.168.132.128:9510/web/rtmp.html
```


### 采坑总结
* nginx 采用多个worker 进程，出现访问不稳定，增加rtmp_auto_push on; 将流推送到不同worker 进程 

### 参考链接
*  [rtmp 官方文档](https://github.com/arut/nginx-rtmp-module)
*  [视频弹幕,参考1](https://github.com/jp9000/OBS)
*  [视频弹幕,参考2](https://github.com/lonelymoon/DanMuer)
*  [OBS 简单使用](https://help.aliyun.com/document_detail/45212.html)
*  [关于视频帧基本概念](https://zhuanlan.zhihu.com/p/23531863)
