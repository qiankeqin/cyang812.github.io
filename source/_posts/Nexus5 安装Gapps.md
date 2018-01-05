title: Nexus5 安装 Gapps
date: 2018/01/05 23:21:23
tags:
- Nexus5
- Android
- Gapps
categories:
- 总结
---

# 一、前言
Nexus5的第三方ROM非常多，这些第三方ROM一般都不带有某种服务（你懂就好）。一般可通过刷入OpenGapps项目提供的插件包来实现。

目前使用的魔趣ROM，刷机完成后，system分区已经被使用了93%，Nexus5的 system分区约为1G，这就导致了连体积最小的 OpenGapps pico包都无法安装。返回错误70，表示没有足够的空间安装文件。

<!-- more -->

# 二、解决方法
查看log文件，这个文件在sdcard分区根目录。可看见如下内容，说明了要安装这个项目至少还需要多少的空间。我这里是还需要大约50M。
```c
         Total System Size (KB) | 1033516
         Used System Space (KB) | 961276
        Current Free Space (KB) | 72240
 Additional Space Required (KB) | 50744   << See Calculations Below
```

了解了这一点后，下一步就是精简系统了。主要的方法是删除一些预装但是又不怎么用的软件。魔趣本身已经是非常精简的系统了，没有什么第三方应用，可以优化减小的空间不大，但是50M找一找也还是可以的。

根据[魔趣论坛](https://bbs.mokeedev.com/t/topic/151)给出的可以删除的系统软件来看，我删除了谷歌国际输入法（33M）,宙斯盾，AudioFX，Email等几个软件。安装所需的50M空间马上就富余出来了。

删除系统软件的方法就是获取ROOT权限以后，使用类似RE管理器之类的软件，进入system分区，找到程序的路径，将整个文件夹删除，重启就好。一般系统预装程序在如下两个路径可以找到：
```
/system/priv-app/Aegis
/system/app
```

如果是后装的软件，一般是在`/data/app`路径下。

使用这个方法精简系统，能够满足OpenGapps 最轻量的pico包成功刷入某种服务就好。这个轻量包是非常精简的，除了基础的组件外只有一个play商店。其他的全家桶应用都可以从这个商店下载后安装，并不影响。其实，这个轻量包中，有个20几M的TTL文本转语音是不常用的，可以在安装后删除，以节省system分区的空间。