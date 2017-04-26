---
title: WIN10 更新系统后，串口无法连接
date: 2017-04-26 15:41:19
tags:
- UART
- Win10
categories:
- 总结
---
   
# 一、问题
- 1、WIN10 更新系统后，无法连接 ST 开发板上 USB 转串口，但可以正常的下载程序。
![这里写图片描述](http://img.blog.csdn.net/20170426093429829?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

<!-- more -->

# 二、解决方法
- 1、尝试更改 COM 口，例如从 COM3 转为 COM4。
![这里写图片描述](http://img.blog.csdn.net/20170426094024912?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- 2、尝试更新 ST-LINK 的固件版本，如下：
![这里写图片描述](http://img.blog.csdn.net/20170426093638831?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


之后便可正常使用了：
![这里写图片描述](http://img.blog.csdn.net/20170426094105647?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)