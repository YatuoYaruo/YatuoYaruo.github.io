---
layout: post
title: android asynctask用法
date: 2018-1-15
tags: [android, java]
---

今天这节课主要是学习了利用android asynctask来进行异步下载的小程序

### UI布局 ###

布局上有一个progressbar,一个button,下面有一个textView
![](/assets/asynctask/1.jpg)

### 重载AsyncTask ###

在MainActivity.java中建立一个DownloadAsyncTask类继承与AsyncTask,然后我们要重载里面的几个方法<br>
首先 doInBackground()方法是我们执行异步操作的地方，这个方法是在子线程中实现的，onPreExecute()方法是在执行之前的操作，onPostExecute()方法是doInBackground()之后的操作,这两个方法都是在主线程中实现的,onProgressUpdate()是实现更新操作<br>
现在我们要在doInBackground()中实现获取网络数据，并下载到本地的操作
>```bash
>@Override
>        protected Boolean doInBackground(String... strings) {
>            if(strings != null && strings.length > 0) {
>                String apkUrl = strings[0];
>
>                try {
>                    URL url = new URL(apkUrl);
>                    URLConnection urlConnection = url.openConnection();
>
>                    InputStream inputStream = urlConnection.getInputStream();
>//获取apk总大小
>                    int contentLength = urlConnection.getContentLength();
>
>                    FILE_NAME = "imooc.apk";
>                    String mFilePath = Environment.getExternalStorageDirectory() + File.separator + FILE_NAME;
>//创建下载路径
>                    File apkFile = new File(mFilePath);
>                    if(apkFile.exists()){
>                        boolean result = apkFile.delete();
>                        if(result){
>                            return false;
>                        }
>                    }
>
>                    int downloadSize = 0;
>
>
>                    byte[] bytes = new byte[1024];
>
>                    int length;
>
>                    OutputStream outputStream = new FileOutputStream(mFilePath);
>
>                    while ((length = inputStream.read(bytes))!= -1) {
>                        outputStream.write(bytes, 0, length);
>                        downloadSize += length;
>//更新进度条
>                        publishProgress(downloadSize * 100/contentLength);
>                    }
>
>                    inputStream.close();
>                    outputStream.close();
>                } catch (MalformedURLException e) {
>                    e.printStackTrace();
>                } catch (IOException e) {
>                    e.printStackTrace();
>                }
>            }
>            return true;
>        }
>```

在onProgressUpate()中实现更新进度条的操作

>```bash
>@Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);
            mProgressBar.setProgress(values[0]);
        }
>```

### 设置Button点击事件 ###
>```bash
>private void setListener() {
>        mButton.setOnClickListener(new View.OnClickListener() {
>            @Override
>            public void onClick(View view) {
>                DownloadAsyncTask asyncTask = new DownloadAsyncTask();
>                asyncTask.execute(APK_URL);
>            }
>        });
>    }
>```

这样当点击按钮是会开始下载并显示进度
