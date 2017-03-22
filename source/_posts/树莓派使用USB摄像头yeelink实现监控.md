title: 树莓派使用USB摄像头和yeelink实现监控
date: 2017/1/1 10:08:23
tags:
- 树莓派
categories:
- 树莓派
---

## 原理：
树莓派安装官方raspbian系统，安装USB摄像头驱动，定时获取摄像头数据保存为图片，上传至yeelink服务器。

<!-- more -->

## 工具：
- 1、树莓派
- 2、USB摄像头
- 3、yeelink账号

## 实现：
- 1、注册yeelink账号，获取用户Api-key。
- 2、添加设备，添加图像传感器。
- 3、树莓派安装fswebcam软件。
- 4、获取图像，通过curl的方式上传。

## 附录：
- 1、查看图片的方式可以是网页，官方手机客户端，API,关于API的使用可参看官方文档。
- 2、yeelink平台对于免费设备，不对数据进行加密，并且有容量和上传时间间隔的限制。
- 3、可使用linux系统下的百度云客户端，将本地的图片数据备份到百度云。

## 代码：
```bash
sudo fswebcam --save /home/pi/class/baiduyun/yeelink.jpg
curl --request POST --url http://api.yeelink.net/v1.0/device/xxxxx/sensor/xxxxxxx/photos --data-binary @"/home/pi/class/baiduyun/yeelink.jpg" --header "U-Apikey: xxxxxxxxxxxxxxxx"
cd /home/pi/class/baiduyun
var=$(date +%y.%m.%d_%H.%M.%S)
mv yeelink.jpg ${var}.jpg
bypy.py syncup
echo ok
```

- 1、首先是将图片保存下来，之后通过curl方式上传。
- 2、然后是将这张照片重新命名为当下时间。
- 3、使用百度云将照片盘上传至百度云空间。


## 参考链接；
- 1、http://blog.yeelink.net/?p=468
- 2、http://www.yeelink.net/developer/apidoc/12
- 3、http://www.zhengyali.com/?p=126
- 4、https://github.com/houtianze/bypy
