# MediaSDK

[https://github.com/JeffMony/MediaSDK.git](https://github.com/JeffMony/MediaSDK.git)

## Android 平台视频边下边播技术

### 1.边下边播技术介绍

我们熟知的边下边播技术，是迅雷提供的，还有之前的快播、快车等工具，它们使用的技术基本上都是P2P下载技术；
P2P下载技术，本质上它并不是C-S的架构，P2P----> Peer to Peer，实际上它将各个客户端的资源调度起来，给上传资源种子，方便后续的下载者可以快速有效的下载资源，这种方式需要服务器整合各个Client，在有用户需要下载的情况下，服务器能及时调度资源，开始给下载者提供资源信息，保证下载者下载资源越快越好；
对一个普通开发者而言，我不想这么费事，我能访问视频资源服务器，我直接从视频源服务器上下载不行吗？是可以的；
视频下载和视频播放本来是两件完全不相干的事情，但是也有共通之处：播放视频的同时就是需要请求视频资源的；
我们要实现边下边播，那就要在请求完视频资源的时候，给播放器送去数据，也要存在本地一份，这样才是边下边播；如何实现边下边播；

### 2.边下边播技术演进

![image](https://user-images.githubusercontent.com/87458342/128356606-161a32fd-dc99-47ed-abf0-ae979837df72.png)

正常的模型是**播放器 <----> 视频源服务器**模型，播放器请求视频资源，视频源服务器收到了请求，返回相应的数据，播放器播放视频数据，这种情况下，也是可以做边下边播的，但是有限制；限制主要是边下边播的控制逻辑非常复杂，因为边下边播的逻辑和播放器的控制逻辑势必搞在一起，这样不仅从架构上无法区分，而且代码上也不好分开，后续维护的成本比较高；一般情况下不建议这么做；

**播放器 <----> 代理服务器 <----> 视频源服务器**

这是提出的一个改进的想法，改进的一个点就是在 播放器 和 视频源服务器之间架了 一个 代理服务器，代理服务器请求 视频源服务器数据，然后返回给播放器，这下就实现了将播放模块与下载模块隔离开来；
但是代理服务器是需要服务器配置的，一般公司没必要搞视频源代理服务器，太耗带宽了。

本地代理服务器替代一下这个代理服务器是比较好的一种方法，既可以实现将播放模块和下载模块分层，也可以实现边下边播的功能。
这就是演进到**播放器 <----> 本地代理服务器 <----> 视频源服务器**

我们本文所讲的 边下边播的技术就是 基于本地代理服务展开的。

## 3.边下边播技术点解析

### 3.1 视频类型
视频类型，我们知道有整视频和分片视频区分；
像 mp4 mov mkv avi rmvb 这些封装格式都是整视频，一般情况下，播放器可以一次请求，后续处理，这些视频最终会存储到一个文件中；
像现在mp4的封装格式应用的最广泛，有一个很优秀的开源库：[https://github.com/danikula/AndroidVideoCache](https://github.com/danikula/AndroidVideoCache)
主要是针对整视频的边下边播来的；

分片视频，就是一个整视频被分为若干个小分片视频，请求的时候不能一次性地请求所有的视频文件；
HLS就是分片视频的典范，关于HLS是什么类型，可以参考文章---->HLS格式解析

```C++
#EXTM3U
#EXT-X-TARGETDURATION:10

#EXTINF:9.009,
http://media.example.com/first.ts
#EXTINF:9.009,
http://media.example.com/second.ts
#EXTINF:3.003,
http://media.example.com/third.ts
#EXT-X-ENDLIST
```

这个HLS文件中有3个分片ts视频，我们请求的时候，需要一个一个请求，整视频请求数据是一次就可以的，后续使用206分段下载；

实现mp4 等整视频的边下边播是可以的，那么HLS分片视频如何实现边下边播呢？

### 3.2 分片视频如何处理

HLS视频----> HTTP Live Streaming，就是熟知的M3U8视频；
[https://tv.youkutv.cc/2019/10/28/6MSVuLec4zbpYFlj/playlist.m3u8](https://tv.youkutv.cc/2019/10/28/6MSVuLec4zbpYFlj/playlist.m3u8)
什么样算是HLS类型的视频？
![image](https://user-images.githubusercontent.com/87458342/128356986-1284e0d2-32c0-42e1-a397-67feab85ef60.png)

主要是请求视频url的Content-Type 数据：
```C++
public static String MIME_TYPE_M3U8_1 = "application/vnd.apple.mpegurl";
public static String MIME_TYPE_M3U8_2 = "application/x-mpegurl";
public static String MIME_TYPE_M3U8_3 = "vnd.apple.mpegurl";
public static String MIME_TYPE_M3U8_4 = "applicationnd.apple.mpegurl";
```

如果发现是上面四种类型，就是M3U8类型的视频，我们就可以按照M3U8解析的规则来解析这个视频url；

解析的视频url是：
[http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.main.m3u8](http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.main.m3u8)
这个m3u8文件存储信息是：

```C++
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11.0
#EXTINF:10.12,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_0_10.ts
#EXTINF:9.88,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_10_20.ts
#EXTINF:10.08,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_20_30.ts
#EXTINF:10.28,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_30_40.ts
#EXTINF:9.68,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_40_50.ts
#EXTINF:10.12,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_50_60.ts
#EXTINF:3.04,
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_60_63.ts
#EXT-X-ENDLIST
```

转化成一个本地代理的文件：

```C++
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11.0
#EXTINF:10.12,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_0_10.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_0.ts
#EXTINF:9.88,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_10_20.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_1.ts
#EXTINF:10.08,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_20_30.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_2.ts
#EXTINF:10.28,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_30_40.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_3.ts
#EXTINF:9.68,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_40_50.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_4.ts
#EXTINF:10.12,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_50_60.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_5.ts
#EXTINF:3.04,
http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_60_63.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_6.ts
#EXT-X-ENDLIST
```

请求一个http://127.0.0.1:3888/http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_0_10.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_0.ts
这个http://127.0.0.1:3888的url会被拦截，直接解析出后续的参数：
http%3A%2F%2Fvideoconverter.vivo.com.cn%2F201706%2F655_1498479540118.mp4.f10_0_10.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_0.ts

对这个url decode 一下：
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_0_10.ts&jeffmony&/3bfd0b2eec722da9ed67509a9388dbe2/seg_0.ts

我们自己定义的一个隔离字符串 &jeffmony&
这个分隔符将字符串分为两部分：
http://videoconverter.vivo.com.cn/201706/655_1498479540118.mp4.f10_0_10.ts

/3bfd0b2eec722da9ed67509a9388dbe2/seg_0.ts

第一个表示当前分片视频的网络url；第二个表示当前文件本地存储的位置；

我们在解析的时候，先判断是否存在本地的分片视频，如果存在，直接读取本地的文件，如果不存在，那要去请求网络的分片url；

最终缓存文件夹下内容如下：

```C++
PD1710:/sdcard/Android/data/com.android.media/cache/.local/3bfd0b2eec722da9ed67509a9388dbe2 $ ls
proxy.m3u8 remote.m3u8 seg_0.ts seg_1.ts seg_2.ts seg_3.ts seg_4.ts seg_5.ts seg_6.ts video.info
```
分片视频都下载到了本地；

真正下载的逻辑应用不需要介绍了，这个大家直接看代码吧；

### 3.3 整视频分段如何处理

视频播放不是孤立的行为，用户有可能会拖动进度条的，拖动进度条，如何拖动到当前没有下载到的位置，那就必须要从拖动到位置向后重新下载，这个分段缓存片段的管理也是比较重要的；

![image](https://user-images.githubusercontent.com/87458342/128357302-c55a6053-ac97-41f8-997d-444142ffd147.png)

用户随意拖动进度条，可能会产生若干个分段的缓存块，这些缓存块是不连续的，但是一旦用户拖动进度条到之前的某个位置，下载资源的时候会将各个分段的缓存块连起来，连接起来之后就是一个完整的视频；

不过维护缓存块的逻辑是比较重要的，这儿主要讲解一下思想，具体看下项目代码吧，是按照上面阐述的思想来的；

### 3.4 边下边播架构图

下面是整个项目的架构图：

![image](https://user-images.githubusercontent.com/87458342/128357393-da0eeb02-bd51-4694-b811-f3152c66bfa7.png)

视频边下边播的库 ---- [https://github.com/JeffMony/MediaLocalProxyLib](https://github.com/JeffMony/MediaLocalProxyLib)

演示效果
![image](https://user-images.githubusercontent.com/87458342/128357524-2e927dab-eefa-4638-97dd-86023257aa9a.png)







