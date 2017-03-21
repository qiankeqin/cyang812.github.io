title: coolweather Debug
tags:
- 编程
- java
- Android
- 第一行代码
categories:
- Android
---
# coolweather Debug

考试周以前照着《第一行代码》写的程序，一直存在bug，开始是直接闪退，后来找到两个地方代码打错后，就没看。过了二十多天后，今晚重新开始，发现可以运行，实现了部分功能，但还是有部分功能出错。可以实现从选择省，选择市，但是不能选择县。选择了具体市后应该要出现的这个市包含那些县，但是这个过程中闪退了。
闪退原因经过后面排查才发现还是打错的四个字母坏的事。不过，发现并解决这个问题的过程很有趣。我在群里发了一个问题，然后有一个人一直帮我解决，后面还远程控制我的电脑，帮我排查。从他排查问题的方法里，学到了很多。

首先就是要弄懂这个闪退后提示的错误代码是什么含义，然后可以通过logcat输出程序内部变量的变化。也就是从这一步步变量变化的过程中，我们最终一起找到了错误。

一开始的错误代码是这样的
```
java.lang.NullPointerException: Attempt to invoke virtual method 'java.lang.String java.lang.Object.toString()' on a null object reference
                                                                          at android.widget.ArrayAdapter.createViewFromResource(ArrayAdapter.java:401)
                                                                          at android.widget.ArrayAdapter.getView(ArrayAdapter.java:369)
                                                                          at android.widget.AbsListView.obtainView(AbsListView.java:2346)
                                                                          at android.widget.ListView.makeAndAddView(ListView.java:1876)
                                                                          at android.widget.ListView.fillSpecific(ListView.java:1355)
                                                                          at android.widget.ListView.layoutChildren(ListView.java:1663)
                                                                          at android.widget.AbsListView.onLayout(AbsListView.java:2148)
                                                                          at android.view.View.layout(View.java:16636)
                                                                          at android.view.ViewGroup.layout(ViewGroup.java:5437)
                                                                          at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1743)
                                                                          at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1586)
                                                                          at android.widget.LinearLayout.onLayout(LinearLayout.java:1495)
                                                                          at android.view.View.layout(View.java:16636)
                                                                          at android.view.ViewGroup.layout(ViewGroup.java:5437)
                                                                          at android.widget.FrameLayout.layoutChildren(FrameLayout.java:336)
                                                                          at android.widget.FrameLayout.onLayout(FrameLayout.java:273)
                                                                          at android.view.View.layout(View.java:16636)
                                                                          at android.view.ViewGroup.layout(ViewGroup.java:5437)
                                                                          at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1743)
                                                                          at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1586)
                                                                          at android.widget.LinearLayout.onLayout(LinearLayout.java:1495)
                                                                          at android.view.View.layout(View.java:16636)
                                                                          at android.view.ViewGroup.layout(ViewGroup.java:5437)
                                                                          at android.widget.FrameLayout.layoutChildren(FrameLayout.java:336)
                                                                          at android.widget.FrameLayout.onLayout(FrameLayout.java:273)
                                                                          at com.android.internal.policy.PhoneWindow$DecorView.onLayout(PhoneWindow.java:2678)
                                                                          at android.view.View.layout(View.java:16636)
                                                                          at android.view.ViewGroup.layout(ViewGroup.java:5437)
                                                                          at android.view.ViewRootImpl.performLayout(ViewRootImpl.java:2171)
                                                                          at android.view.ViewRootImpl.performTraversals(ViewRootImpl.java:1931)
                                                                          at android.view.ViewRootImpl.doTraversal(ViewRootImpl.java:1107)
                                                                          at android.view.ViewRootImpl$TraversalRunnable.run(ViewRootImpl.java:6013)
                                                                          at android.view.Choreographer$CallbackRecord.run(Choreographer.java:858)
                                                                          at android.view.Choreographer.doCallbacks(Choreographer.java:670)
                                                                          at android.view.Choreographer.doFrame(Choreographer.java:606)
                                                                          at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:844)
                                                                          at android.os.Handler.handleCallback(Handler.java:739)
                                                                          at android.os.Handler.dispatchMessage(Handler.java:95)
                                                                          at android.os.Looper.loop(Looper.java:148)
                                                                          at android.app.ActivityThread.main(ActivityThread.java:5417)
                                                                          at java.lang.reflect.Method.invoke(Native Method)
                                                                          at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:726)
                                                                          at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:616)
                                                                          at de.robv.android.xposed.XposedBridge.main(XposedBridge.java:114)

```
这个错误代码的意思是adapter适配器错误，接收到的null对象。然而前面的省和市都已经正常显示出来了，所以一定的县这个数据列表的错误。
因此在获取县数据列表中加入log.e，如下
```
Log.e("TAG","datalist="+ dataList.toString());
```
从数据返回的结果来看确实为null,因此，可见数据没有获得。而从浏览器测试API的结果来看，数据是可以正常获取的。因此，错误应该在解析上出错。

找到解析返回数据的函数，加入log.e,如下
```
Log.e("TAG","DDD="+response);
```
从返回的数据来看，是正常从服务器接受了数据的，数据的格式为`E/TAG: DDD=060601|白城,060602|洮南,060603|大安,060604|镇赉,060605|通榆`.而解析出的结果为两个字符串，一个为城市代号，一个为城市名称。这两个分别加入数组的不同位置。而出错就是在这里。如下：
正确代码：
```
county.setCountyCode(array[0]);
county.setCountyName(array[1]);
```
错误代码：
```
county.setCountyCode(array[0]);
county.setCountyCode(array[1]);
```
就是Name错写成Code，所以整个程序就出错了。
