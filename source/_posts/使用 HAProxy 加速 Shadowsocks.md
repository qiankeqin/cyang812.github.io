title: 使用 HAProxy 加速 Shadowsocks
date: 2017/1/12 11:48:23
tags:
- Shadowsocks
- HAProxy
- 腾讯云
- 梯子
- 服务器
- vps
categories:
- 教程
---

前几天一篇放在CSDN介绍搭梯子的文章被删除了，把文章重新放在个人博客后，又重新用起了自己的SS服务，发现速度不如之前了，可能是vps服务供应商的问题。之前在电脑端比较常使用XX-Net的梯子，手机端使用的也是别人的SS服务，因此很少使用自己的SS服务。不过，两天前，手机上一直使用的SS服务被开发者停用了，所以只好用回自己的。也因此有了这篇使用 HAProxy 加速 shadowsocks 的教程。关于 shadowsocks 服务的搭建，[可以看这篇文章。](http://cyang.tech/2017/01/08/38%E5%9D%97%E6%90%9E%E5%AE%9A%E4%B8%80%E5%B9%B4%E7%9A%84vps%E5%92%8Cshadowsocks%E6%9C%8D%E5%8A%A1/)

<!-- more -->

# 一、必备工具
- 1、自建的SS服务
- 2、国内的服务器（阿里云，腾讯云之类的）

# 二、安装步骤
>下面的步骤是基于我在腾讯云的服务器，系统是centos 6.7 64位。

- 1、安装 HAProxy
使用xshell远程登陆centos，使用以下指令安装
```
yum install haproxy
```
- 2、修改配置文件
默认的配置文件有很多，其实只要修改为如下内容就好：
  ```
  global
      ulimit-n  51200

  defaults
  	log	global
  	mode	tcp
  	option	dontlognull
          timeout connect 5000
          timeout client  50000
          timeout server  50000

  frontend ss-in
      bind *:6666 #腾讯云端口
      default_backend ss-out

  backend ss-out
      server server1 233.233.233.233:8388 maxconn 20480 #SS服务的ip和端口
  ```

- 3、启动 haproxy 服务

  #启动
  ```
  /etc/init.d/haproxy start
  ```
  #停止
  ```
  /etc/init.d/haproxy stop
  ```

# 三、注意事项
- 1、测试结果为在电脑端速度提升不明显，但是使用手机是速度提升还是很明显的。
- 2、卸载 haproxy

  #卸载
  ```
  yum -y remove haproxy
  yum -rf /etc/haproxy
  ```

# 四、参考链接
- 1、[haproxy 加速 shadowsocks](http://zhangyongcun.com/2016/11/15/haproxy-%E5%8A%A0%E9%80%9F-shadowsocks/)
- 2、[Shadowsocks利用 HaProxy 实现中继(中转/端口转发)](https://doub.io/ss-jc29/)
