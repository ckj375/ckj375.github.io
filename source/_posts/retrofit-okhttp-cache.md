---
title: Retrofit + OkHttp + RxJava系列教程(二)：缓存处理
date: 2017-09-12 10:11:33
tags: 网络请求
categories: Android
---
通过缓存处理可以有效降低服务器的负荷,加快APP界面加载速度,提升用户体验。Retrofit + OkHttp缓存处理流程是这样的,请求响应之后会在data/data/packageName/cache下建立一个response文件夹，保存缓存数据,后续请求时若无网络,则直接读取缓存内容,若有网络则从网络获取最新数据并缓存。

1.设置缓存路径,大小及添加缓存拦截器
```
//设置缓存路径
File httpCacheDirectory = new File(CommonApplication.getInstance().getCacheDir(), "responses");
//设置缓存 10M
Cache cache = new Cache(httpCacheDirectory, 10 * 1024 * 1024);
//创建OkHttpClient，并添加拦截器和缓存代码
OkHttpClient client = new OkHttpClient.Builder()
        .addNetworkInterceptor(new CacheInterceptor(CommonApplication.getInstance()))
        .cache(cache).build();
```
<!-- more -->
2.定义缓存拦截器。若网络正常,则缓存有效期1分钟;若网络异常,则缓存有效期6小时
```
public class CacheInterceptor implements Interceptor {

    private Context mContext;

    public CacheInterceptor(Context context) {
        mContext = context;
    }

    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();

        if (NetworkUtils.isNetworkAvailable(mContext)) {//没网强制从缓存读取(必须得写，不然断网状态下，退出应用，或者等待一分钟后，就获取不到缓存）
            request = request.newBuilder()
                    .cacheControl(CacheControl.FORCE_CACHE)
                    .build();
        }
        Response response = chain.proceed(request);
        Response responseLatest;
        if (NetworkUtils.isNetworkAvailable(mContext)) {
            int maxAge = 60; //有网失效一分钟
            responseLatest = response.newBuilder()
                    .removeHeader("Pragma")
                    .removeHeader("Cache-Control")
                    .header("Cache-Control", "public, max-age=" + maxAge)
                    .build();
        } else {
            int maxStale = 60 * 60 * 6; // 没网失效6小时
            responseLatest = response.newBuilder()
                    .removeHeader("Pragma")
                    .removeHeader("Cache-Control")
                    .header("Cache-Control", "public, only-if-cached, max-stale=" + maxStale)
                    .build();
        }
        return responseLatest;
    }
}
```
