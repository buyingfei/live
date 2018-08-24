# 从零开始搭建直播平台

### 项目实现目标
1. 播放本地视频
2. 播放远程视频流
3. 使用rtmp 格式录视频，并播放
4. 使用hls 格式录视频，并播放

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

#### 参考链接
*  [rtmp 官方文档](https://github.com/arut/nginx-rtmp-module)
*  [视频弹幕,参考1](https://github.com/jp9000/OBS)
*  [视频弹幕,参考2](https://github.com/lonelymoon/DanMuer)
