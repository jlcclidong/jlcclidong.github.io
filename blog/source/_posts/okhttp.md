---
title: OkHttp3-从零开始的详细使用到简单封装
categories:
- library
tags:
- okhttp
- library
---
# [Github地址---MyOkhttpUtils](https://github.com/jlcclidong/MyOkhttpUtils)
## 初衷
对于okhttp3出来以后，使用的都是网上封装好的库，为了更好地理解封装的内容，对okhttp本身的熟悉，肯定学一下子了，网上okhttp的方法很多，但是网上okhttp3方面的东西并不多，很多都过时的方法，总结了一下，讲了一下okhttp3的基本使用方法，在下边要耐心才会 。
封装了一下基本大家使用得到的功能，想用的话可以直接拿去用，想扩展的话也可以直接下载扩展，代码比较少，不了解okhttp的可以下载学习一下，大神门口右走吧。
封装逻辑和使用的好多方法找不到，借鉴了两位大神的逻辑，相当于简化版本吧，封装的比较全面的可以参照
[hongyangAndroid / okhttputils](https://github.com/hongyangAndroid/okhttputils)
[jeasonlzy/okhttp-OkGo](https://github.com/jeasonlzy/okhttp-OkGo)
## 本文中库的简单使用介绍
* 在project中添加依赖
````java
  allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
     ````
* 在app中添加依赖
````java
        dependencies {
  	        compile 'com.github.jlcclidong:MyOkhttpUtils:0.10'
	    }
      ````
<!-- more -->
* 在本项目中使用okhttp的方法步骤
在application中初始化，此步骤必须，别忘记在清单文件中注册application否则不能使用
````java
       Ok.init(this)
                .connectTimeout(3000l, TimeUnit.MILLISECONDS)
                .readTimeout(3000l, TimeUnit.MILLISECONDS)
                .commonParams("haha","1") //添加公共参数
                .commonHeader("nihao","1")  //添加公共头
                //這兩個攔截器衹能選用一個
                //.AppInterceptor("eason", new LogInterceptor())   //应用拦截器
                .NetWorkInterceptor("eason",new LogInterceptor()) //网络拦截器 将重定向等的request response页拦截打印  
                .CookieJar(new PersistentCookieJar(new SetCookieCache(), new SharedPrefsCookiePersistor(App.this)))
                .build();
````
* 请求  其中callback有三种类型 fail都会返回Exception，不管是本身网络问题还是404等问题都会返回此回调，可以通过Exception类型进行自行处理
  * callback有三种类型
    * JsonCallback<T> 传入类自动转换成bean，但是并未处理超大Json文件OOM 可自行通过流的方式来处理解析
    * CallBack 返回String
    * FileCallBack(String dir, String filename) 其中传入文件夹和文件名来保存 返回进度(0-100)
 * get
 ````java
        Ok.get().url("http://lab.zuimeia.com/wallpaper/category/1/")
                .param("page_size", 1)
                .build()
                .call(new JsonCallBack<Bean>() {  //傳入實體類
                    @Override
                    public void fail(Exception e) {

                    }

                    @Override
                    public void success(Bean bean) {
                        mTv.setText(bean.getData().getBase_url());
                    }
                });
                ````
 * post  支持键值对和文件表单上传
 ````java
        Ok.post().url("https://www.baidu.com")
                .param("test", 1)
                .file("test", new File(""))
                .build()
                .call(new CallBack() {
                    @Override
                    public void fail(Exception e) {

                    }

                    @Override
                    public void success(String response) {
                        mTv.setText(response);
                    }
                });
                ````
  * postjson  支持postJSON
```java
      Ok.postJson().url("")
          .json("")
          .build()
          .call(new CallBack() {
                @Override
                  public void fail(Exception e) {

                }

                @Override
                public void success(String response) {

                }
            });
   ```
  * download 文件下载只做到最基本的下载,断点续传和暂停下载暂未做，后续会追加
  ```java
         Ok.download().url("http://static.oschina.net/uploads/space/2015/0629/170157_rxDh_1767531.png")
                .build()
                .tag(MainActivity.this)
                .call(new FileCallBack(getCacheDir().getAbsolutePath(), "github.png") {
                    @Override
                    public void progress(int progress) {
                        mTv.setText(progress + "");
                    }

                    @Override
                    public void success(File file) {
                        mIV.setImageBitmap(BitmapFactory.decodeFile(file.getAbsolutePath()));
                    }

                    @Override
                    public void fail(Exception e) {

                    }
                });
  ```
  * 取消
````java
         Ok.cancle(MainActivity.this);
         ````
 * 同时支持PostJson() 和 PostFile()两种方法
* 其中日志打印时使用了格式化JsonFormat工具类
* 请求时自动添加了 Accept-Language 和 User-Agent
* 简单封装没有太多的代码，可以自行下载扩展
## okhttp本身的基本使用
* 初始化一个全局的okhttpclient
````java
         OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .connectTimeout(3000l, TimeUnit.MILLISECONDS)
                .readTimeout(3000l, TimeUnit.MILLISECONDS)
                .addInterceptor( new LogInterceptor())
                .cookieJar(...)
                .build();
                ````
当然初始化的方法有很多这里只是列出常用的方法，okhttp建议全局使用同一个
* 为了更好的理解get，post方法，我们可以写一个interceptor来监听request和response，实现这个类不可以出现问题否则是无法进行正常请求的
可以在此处修改request和response的参数添加缓存设置等，可自行添加
主要方法有这样几个，必须重写的方法
````java
       @Override
        public Response intercept(Chain chain) throws IOException {
          Request request = chain.request();
          logForRequest(request);
          return logForResponse(response, time);
        }
        ````
打印request没有什么特别注意的地方
````java
      private void logForRequest(Request request) {
        try {
            Log.e("============request start===============");
            Log.e("url:" + request.url());
            Log.e("method:" + request.method());
            if (request.headers() != null && request.headers().size() > 0)
                Log.e("headers:" + request.headers().toString());
            Log.e("============request end=================");
        } catch (Exception e) {
            Log.e("log request has something worng!!");
        }
        }
        ````
打印response时注意，如果添加了interceptor则不能直接调用response.body().string()方法，调用此方法会直接close掉这个response，在callback中得不到正确的结果会报异常，只能使用response.newbuilder() 方法来使用新创建的response调用，eg
````java
        private Response logForResponse(Response response, long time) {
        try {
            Log.e("============response start==============");
            //response.body().string()只能调用一次 body()就会关掉
            //每次使用前都clone一份使用保证原来的body没有被关掉
            Response copy = response.newBuilder().build();
            Log.e("responseurl:" + copy.request().url());
            Log.e("response code:" + copy.code());
            Log.e("total time:" + time);
            if (!TextUtils.isEmpty(copy.message()))
                Log.e("message:" + copy.message());
            if (copy.headers() != null && copy.headers().size() > 0)
                Log.e("headers:" + copy.headers().toString());
            Log.e("============response end================");
            ResponseBody body = copy.body();
            if (body != null) {
                MediaType mediaType = body.contentType();
                if (mediaType != null) {
                    Log.e("============response body===============");
                    Log.e("Content-type:" + mediaType.toString());
                    if (isText(mediaType)) {
                        String content = body.string();
                        Log.e(JsonFormat.formatJson(content));
                        Log.e("============response body===============");
                        return response.newBuilder().body(ResponseBody.create(mediaType, content)).build();
                    } else {
                        Log.e(" maybe response content too large too print , ignored!");
                    }
                }
            } else {
                Log.e(" body is null , ignored!");
            }
        } catch (Exception e) {
            Log.e("log response has something worng!!");
        }
        return response;
      }
      ````
这样每次调用http请求是就可以详细监听其中的内容了
全部具体内容详见github代码
* okhttp请求就是构建一个request加入到okhttpclicent中就可以了
  这里实例只讲okhttp的异步请求，当然同步只是将enqueue()方法替换成execute()
注意这个回调的onFailure只是回调了本身请求的一些失败回调，例如超时IOException....当成功返回之后需要在onResponse中判断response.code()来判断是否是200，404，500之类的统一处理
  * get
  ```java
        Request request = new Request.Builder().get()
                .url("https://www.baidu.com")
                .header("test","fds")
                .build();
        okHttpClient.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {

            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {

            }
        });
        ```
 * post
只需要替换request post中需要传一个requestBody，不同的post请求，需要不同的请求体，当然只是方便我们不同的需求。
基本的键值对上传在下面贴出，使用FormBody即可创建
键值对加上文件上传则需要MultipartBody来创建
而普通的上传json，上传文件可以直接调用RequestBody的静态方法来创建
````java
        Request request = new Request.Builder().post(new FormBody.Builder().add("sdf","Sdfa").build())
                .url("https://www.baidu.com")
                .header("test","fds")
                .build();
                ````
其他方法详见本库中request中做的封装，都包含入内了
* https的影响，okhttps本身不添加任何代码就可以支持okhttps中公认的证书，类似于百度之类的，当时像12306这种只能手动添加了，当然也可以默认信任所有证书，具体由于本人公司并不需要就没写,推荐[ [Android Https相关完全解析 当OkHttp遇到Https](http://blog.csdn.net/lmj623565791/article/details/48129405)]
* cookie持久化网上有很多方法，本库采用的是[franmontiel / PersistentCookieJar](https://github.com/franmontiel/PersistentCookieJar)
* okhttp3取消请求
这个是比较坑的网上传统的okhttpclicent.cancle(tag)方法发现没有了，只可以通过call.cancle来取消请求，后来发现是这样子根据tag来取消请求的，当然如果请求已经开始回调无法结束，正在上传或者下载时会产生IOException
````java
        //隊列中的call
        for (Call call : getInstance().dispatcher().queuedCalls()) {
            if (object.equals(call.request().tag())) {
                call.cancel();
            }
        }
        //運行中的call
        for (Call call : getInstance().dispatcher().runningCalls()) {
            if (object.equals(call.request().tag())) {
                call.cancel();
            }
        }
````
最后
好像没什么了 ，感觉有帮助的帮忙点个star，辣眼睛了的我也没办法

![啦啦啦啦.gif](http://upload-images.jianshu.io/upload_images/1905930-18b22cdf6f936fe5.gif?imageMogr2/auto-orient/strip)
