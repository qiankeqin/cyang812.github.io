title: 2048游戏开发
tags:
- 编程
- java
- Android
categories:
- Android
---
# 2048游戏开发

## 项目说明
2048游戏是一款非常简单，容易上手但是又不失趣味性的一款游戏。根据《极客学院》的教学视频学的，视频总共70分钟左右。完成整个项目大概花费两个晚上共计5小时的时间。基本上没有遇到什么比较大的难点，遇到的问题都可以解决。

## 疑点难点
- 1、关于activity的使用。这个应用，仅有一个activity，而在这个activity上没有太多的代码，主要用于更新分数。主要逻辑的代码实现都在其他两个类。这两个类分别为GameView，主要的逻辑实现，实现了交互（左右上下滑动的检测），以及很多具体的函数；另一个为Card类，这个类是一个卡片模型，主要用于实现游戏界面里的数字模型，有set,get,equals方法。
- 2、而这两个类都是继承自Layout布局的。
- 3、关于左右上下滑动的检测代码。这部分额代码，目前从才做上来说看感觉还有可以优化的空间。按照视频里的源码，尽管获取的变脸和算法没有问题，但是使用那样的写法就是无法实现准确的判读。而且目前看来，由于没有动画效果，因此整个交互显得比较僵硬，下一步可以考虑添加动画效果。

## 知识点
- 1、GridLayout是一种网格布局，用在这里正好可以用作4*4的卡片布局。
- 2、FramLayout是一种很简单的布局，没有任何的定位方式，所有的控件都是摆放在布局的左上角。
- 3、监听滑动操作。通过侦听触摸事件，可以很好的判断出用户的操作逻辑。

## 部分代码
- 1、生成2和4的比例为9：1.

`Math.random()>0.1?2:4`

- 2、AlertDialog的使用
```
AlertDialog.Builder dialog = new AlertDialog.Builder(getContext());
            dialog.setTitle("你好");
            dialog.setMessage("重来");
            dialog.setCancelable(false);
            dialog.setPositiveButton("ok", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    startGame();
                }
            }).show();
```
