---
layout: post
title: android 利用Handler实现一个倒计时的小程序
date: 2018-1-13
tags:  [android, java]
---

通过慕课网教程学习 **android Handler** 用法实现一个倒计时的小程序<br>
在上节课创建的Handler可能会导致内存泄露，这节课我们通过 **弱引用** 来创建一个静态的Handler<br>
在xml中创建一个TextView<br>
<br>
创建 **CountdownTimeHandler** 类继承Handler<br>
定义弱引用Activity
>```bash
>final WeakReference<CountDownActivity> mainActivityWeakReference;
>```

重载handleMessage()方法
>```bash
>public void handleMessage(Message msg) {
>            CountDownActivity countDownActivity = mainActivityWeakReference.get();
>            super.handleMessage(msg);
>            switch (msg.what) {
>                case COUNTDOWN_TIME_CODE:
>                    int value = msg.arg1;
>                    countDownActivity.countdownTextView.setText(String.valueOf(value --));
>                    Message message = Message.obtain();
>                    message.what = COUNTDOWN_TIME_CODE;
>                    message.arg1 = value;
>
>                    if (value > 0) {
>                        sendMessageDelayed(message,DELAY_MILLIS);
>                    }
>
>                    break;
>            }
>        }
>```

在Activity onCreate方法内实例化handler
>```bash
>CountdownTimeHandler handler = new CountdownTimeHandler(this);
>
>        Message message = Message.obtain();
>        message.what = COUNTDOWN_TIME_CODE;
>        message.arg1 = MAX_COUNT;
>
>        handler.sendMessageDelayed(message, DELAY_MILLIS);
>```
