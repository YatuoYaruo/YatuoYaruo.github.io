---
layout: post
title: android HttpUrlConnection
date: 2018-1-13
tags: [android, java]
---

通过慕课网教程学习 **android HttpUrlConnection** 用法<br>
这节主要是通过HttpUrlConnection从网络上请求数据并显示到ui上<br>
首先这里提供一个url: "http://www.imooc.com/api/teacher?type=3&cid=1"<br>
它会给我们返回一个json数据如下<br>
{"status":1,"data":{"title":"Tony\u8001\u5e08\u804ashell\u2014\u2014\u73af\u5883\u53d8\u91cf\u914d\u7f6e\u6587\u4ef6","author":"Tony","content":"\u672c\u8bfe\u7a0b\u662f\u300aTony\u8001\u5e08\u804ashell\u300b\u7cfb\u5217\u8bfe\u7a0b\u7684\u7b2c\u4e09\u7bc7\uff0c\u4e3a\u4f60\u5e26\u6765\u5e38\u7528\u7684\u73af\u5883\u53d8\u91cf\u914d\u7f6e\u6587\u4ef6\u7684\u4f7f\u7528\u3002"},"msg":"\u6210\u529f"}
<br>
由于网络请求不可以在主线程进行,所以我们要新建一个线程进行请求<br>
我们首先创建一个url
>URL url = new URL("http://www.imooc.com/api/teacher?type=3&cid=1");

建立连接
>HttpUrlConnection coon = (HttpUrlConnection)url.openConnection();

设定请求类型及连接时间
>coon.setRequestMethod("GET")
>coon.setReadTimeout(6000)

判断状态码
>if(coon.getResponseCode() == 200)

进行数据流操作
>InputStream in = coon.getInputStream();  
>byte[]b = new byte[1024*512];  
>int len = 0;  
>  
>ByteArrayOutputStream baos = new ByteArrayOutputStream();  
>whilte((len = in.read(b)) > -1) {  
>&nbsp;&nbsp;&nbsp;&nbsp;baos.write(b,0,len);  
>}  
>String msg = baos.toString();  
>JSONObject obj = new JSONObject(msg);  

这样我们的json数据就被写到了msg变量里，接下来我们就要解析这个json数据<br>
这里我们用到了一个工具类 **Gson** ,首先我们要在 **build.gradle** 写入
>compile 'com.google.code.gson:gson:2.8.0'

接下来我们开始使用这个Gson类
>int status = obj.getInt("status");  
>String msg2 = obj.getString("msg");  
>String data = obj.getString("data");  
>Gson gson = new Gson();  

我们json数据里data里有title,author,content三个数据,首先我们要创建Essay类
>public class Essay {
>&nbsp;&nbsp;&nbsp;&nbsp;private String title;  
>&nbsp;&nbsp;&nbsp;&nbsp;private String author;  
>&nbsp;&nbsp;&nbsp;&nbsp;private String content;    
>}  

实现构造方法，及get set方法<br>
再回到我们的gson类，解析data
>Essay e = gson.fromJson(data,Essay.class);

这样我们的json数据解析完了，现在我们要将数据展示到ui上<br>
由于我们现在在子进程，不能对ui进行操作，所以我们要将数据传递到主线程中<br>
我们再主线程中创建一个 **Handler**
>private Handler handler = new Handler() {};

在子线程中建立一个 **message** ,将我们的Essay类放进去
>Message message = handler.obtainMessage();  
>message.obj = e;  
>handler,sendMessage(message);

在主线程中的 **handler** 重写 **handleMessage(Message msg)** 方法
>@Override  
>prblic void handleMessage(Message msg) {  
>&nbsp;&nbsp;&nbsp;&nbsp;super.handleMessage(msg);
>&nbsp;&nbsp;&nbsp;&nbsp;Essay e = (Essay)msg.obj;
>}

之后我们就可以将数据展示到ui界面上
