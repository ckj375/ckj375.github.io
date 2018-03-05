---
title: Retrofit + OkHttp + RxJava系列教程(一)：基础篇
date: 2017-09-05 10:50:21
tags: 网络请求
categories: Android
---
Retrofit + OkHttp + RxJava已成为当前Android 网络请求最流行的方式。之前处理网络请求都是在前人搭好的基础上进行维护，主要是新增接口以及处理返回结果等，对整个网络请求过程理解不是很透彻，所以这次自己一步步从头搭建。Retrofit是一个网络请求框架，底层基于OKHttp实现；OkHttp是一个开源的网络请求库； RxJava是一个在JVM上使用可观测的序列来组成异步的、基于事件的程序的库，可以让异步操作变得简单。总而言之：Retrofit负责请求的数据和请求的结果，使用接口的方式呈现，OkHttp负责请求的过程，RxJava负责异步，各种线程之间的切换。
<!-- more -->

下面分开介绍以下两种请求方式：  
一.Retrofit + OkHttp  
二.Retrofit + OkHttp + RxJava

**一.Retrofit + OkHttp**

1.在Gradle配置文件中添加依赖
```
compile 'com.squareup.retrofit2:retrofit:2.2.0'
```

2.创建Retrofit实例
```
private static final String BASE_URL = "http://xxx";
private static Retrofit mRetrofit;

private static Retrofit getRetrofit() {
    if (mRetrofit == null) {
        mRetrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                //.addConverterFactory(GsonConverterFactory.create())
                //.client(getHttpClient())
                .build();
    }

    return mRetrofit;
}

private static OkHttpClient getHttpClient() {
    //设置缓存路径
    File httpCacheDirectory = new File(CommonApplication.getInstance().getCacheDir(), "responses");
    //设置缓存 10M
    Cache cache = new Cache(httpCacheDirectory, 10 * 1024 * 1024);
    //创建OkHttpClient，并添加拦截器和缓存代码
    OkHttpClient client = new OkHttpClient.Builder()
            .addNetworkInterceptor(new CacheInterceptor(CommonApplication.getInstance()))
            .cache(cache).build();
    return client;
}
```
如果请求返回的是Gson格式，则可以配置Gson转换器；如果需要添加拦截器或者配置缓存等，可以通过手动创建OkHttp实例来实现。

3.创建接口类
```
public interface HttpClientService {

    @GET("/hero/{heroid}/")
    Call<ResponseBody> getHero(@Path("heroid") String heroid);

}
```
其中“/hero/{heroid}/”为接口路径，heroid为请求参数。

4.用Retrofit创建接口实例HttpClientService,并且调用接口中的方法进行网络请求
```
HttpClientService mHttpClientService = mRetrofit.create(HttpClientService.class);
Call<ResponseBody> call = mHttpClientService.getHero(heroId);
call.enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {

    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {
        t.printStackTrace();
    }
});
```
以上是异步方式请求，还有同步方式execute(),返回一个Response,代码如下：
```
Response<ResponseBody> response = call.execute();
```

**二.Retrofit + OkHttp + RxJava**

1.在Gradle配置文件中添加依赖
```
compile 'com.squareup.retrofit2:retrofit:2.2.0'
compile 'com.squareup.retrofit2:adapter-rxjava:2.2.0'
compile 'io.reactivex:rxandroid:1.2.1'
```
2.在创建Retrofit实例时须配置RxJavaCallAdapterFactory
```
private static final String BASE_URL = "http://xxx";
private static Retrofit mRetrofit;

private static Retrofit getRetrofit() {
    if (mRetrofit == null) {
        mRetrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
    }

    return mRetrofit;
}
```
3.更改接口定义，返回值不再是Call ,而是Observable
```
public interface HttpClientService {

    @GET("/hero/{heroid}/")
    Observable<ResponseBody> getHero(@Path("heroid") String heroid);

}
```
4.用Retrofit创建接口实例HttpClientService,并且调用接口中的方法进行网络请求
```
HttpClientService mHttpClientService = mRetrofit.create(HttpClientService.class);
mHttpClientService.getHero(heroId)
          .subscribeOn(Schedulers.io())//在io线程订阅
          .observeOn(AndroidSchedulers.mainThread())//在主线程观察
          .subscribe(new Subscriber<ResponseBody>() {

              @Override
              public void onNext(ResponseBody responseBody) {

              }

              @Override
              public void onCompleted() {

              }

              @Override
              public void onError(Throwable e) {
                  e.printStackTrace();
              }
          });
```
