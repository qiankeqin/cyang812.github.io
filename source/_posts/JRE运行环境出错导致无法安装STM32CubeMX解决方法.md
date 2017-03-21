title:  JRE运行环境出错导致无法安装STM32CubeMX解决方法
date: 2017/1/23 15:07:23
tags:
- JRE
- STM32CubeMX
- STM32
categories:
- 总结
---

![STM32CubeMX](http://od68ytlrn.bkt.clouddn.com/%E5%AE%89%E8%A3%85.png)
<!-- more -->

# 一、问题
安装 STM32CubeMX 一直提示需要安装JAVA运行环境，提示界面如下：
![这里写图片描述](http://img.blog.csdn.net/20170123144645074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
但实际上已经正确安装了JRE，如下为JAVA版本。
![这里写图片描述](http://img.blog.csdn.net/20170123144653163?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

问题原因：我的电脑上具有很多版本的JRE，并且有一个版本注册表信息出错，所以无法正常卸载。

# 二、解决方法
- 1、先完整卸载目前已安装的JAVA版本，可利用[官方提供的卸载工具](https://www.java.com/zh_CN/download/faq/uninstaller_toolfaq.xml)。
![这里写图片描述](http://img.blog.csdn.net/20170123144945121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
如上图所示，电脑里安装了4个版本的JRE，只需安装最新版的就好，其他直接卸载。

- 2、如果出现[无法卸载的版本](https://www.java.com/zh_CN/download/help/regkey_addremove.xml)，一般是注册表出错，此时可以使用[微软系统修复工具](https://support.microsoft.com/zh-cn/help/17588/fix-problems-that-block-programs-from-being-installed-or-removed)，也可以手动修改注册表，建议第一种。
![这里写图片描述](http://img.blog.csdn.net/20170123145203374?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
修复之后使用之前的卸载工具可以看到，此时电脑里已经不存在任何版本的JRE。

- 3、再次安装JRE，[根据系统版本下载对应的安装文件](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html)，STM32CubeMX需要的最低版本为1.7.0_45，建议直接安装最新版。
![这里写图片描述](http://img.blog.csdn.net/20170123145703985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


# 三、STM32CubeMX的安装
在正确安装好JRE后，STM32CubeMX的安装非常简单，[官网下载](http://www.st.com/zh/development-tools/stm32cubemx.html?icmp=pf259242_prom_stm32cube-long-promo_feb2014)，解压，双击安装。
![这里写图片描述](http://img.blog.csdn.net/20170123150001800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20170123150043339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20170123150101753?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20170123150332734?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTMwMzQ0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
