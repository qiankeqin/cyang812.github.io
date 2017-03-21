title: countTime倒计时应用(在子线程中更新主UI)
tags:
- 编程
- java
- Android
categories:
- Android
---

# countTime倒计时应用(在子线程中更新主UI)

## 项目说明
通过文本输入框输入数据，通过点击按钮进行控制操作，每隔一秒时间见一。难点在于，在子线程中更新UI会导致程序崩溃。

## 知识点
- 1、当应用程序启动时，Android首先会开启一个主线程 (也就是UI线程) ， 主线程为管理界面中的UI控件， 进行事件分发， 比如说， 你要是点击一个 Button ，Android会分发事件到Button上，来响应你的操作。
- 2、如果此时需要一个耗时的操作，例如: 联网读取数据，或者读取本地较大的一个文件的时候，你不能把这些操作放在主线程中，如果你放在主线程中的话，界面会出现假死现象， 如果5秒钟还没有完成的话，会收到Android系统的一个错误提示  "强制关闭"。  这个时候我们需要把这些耗时的操作，放在一个子线程中，因为子线程涉及到UI更新，，Android主线程是线程不安全的， 也就是说，更新UI只能在主线程中更新，子线程中操作是危险的。
- 3、Handler就出现了，来解决这个复杂的问题 ，由于Handler运行在主线程中(UI线程中)，它与子线程可以通过Message对象来传递数据，这个时候，Handler就承担着接受子线程传过来的(子线程用sedMessage()方法传弟)Message对象，(里面包含数据)，把这些消息放入主线程队列中，配合主线程进行更新UI。
- 4、Timer和TimerTask的使用。这两者都是配合使用的，前者是一个定时器，后者是定时器任务。在定时器任务里面，需要复写run方法，在run方法中，我们需要对时间进行减一操作，并通过message将消息发送送出去，从而让Handler接受消息并更新UI。而对于定时器来说，需要手动开启，并可以设置定时任务和定时时长。而在停止倒计时的方法里，只需要将定时器取消就可以。

## 部分代码
- 1、Handler方法，接受消息，更新主UI
```java
//Handler
    private android.os.Handler mHandler = new android.os.Handler(){

        //接受消息并更新UI
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            showTime.setText(msg.arg1+"");//更新UI
            startTime();//更新完成后再次开始倒计时以便循环
        }
    };
```

- 2、开启倒计时方法，使用定时器。
```java
//开启倒计时
    private void startTime(){
        timer = new Timer();//实例化定时器
        //实例化定时器任务
        timerTask = new TimerTask() {
            @Override
            public void run() {
                //封装消息
                time--;
                Message message = mHandler.obtainMessage();
                message.arg1 = time;
                mHandler.sendMessage(message);
            }
        };
        //开启定时器，设置定时器任务，设置定时时长为1000ms
        timer.schedule(timerTask,1000);
    }
```
