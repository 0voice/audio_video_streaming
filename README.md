<div align=left>

# 音视频流媒体权威资料整理，500+份文章，论文，视频，实践项目，开源框架，协议，业界大神名单。
<!--
本社区致力于从源码层面，剖析和挖掘音视频行业主流技术的底层实现原理，为广大想从事音视频的开发者提供音视频权威，全面，深度的音视频学习社区。
-->
</div>

<br/>

<div align=left>

<font size=4 color=#DC143C>
音视频的知识纷繁复杂，自学非常困难，既需要非常扎实的基础知识，又需要有很多的工程经验；本项目致力于从音视频开发，开源框架，视频，业界大神，paper，书籍协议，文章，实践项目整理素材，为广大开发者学习音视频技术提供便利。
</font>
  
</div>

<br/>
<br/>
<br/>

<div align=center>
  
音视频开发     |开源框架      |视频          |业界大神      | paper        | 书籍协议       |  文章        |实践项目      
:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:
[🎵](#nav_1) | [🌐](#nav_2)|[🧿](#nav_3) |[👀](#nav_3)  |[🍀](#nav_4) |[🔰](#nav_5)    |[🍮](#nav_6) |[🥌](#nav_7)  
  
</div>

<br/>
<br/>
<br/>

<h2 id="nav_1">🎵 音视频开发</h2>

### 1.1 音视频基础知识入门

  ![音视频基础知识入门说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_11.png "音视频基础知识入门") 

### 1.2 FFMPEG命令实战

  ![FFMPEG命令实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_21.png "FFMPEG命令实战") 

### 1.3 FFMPEG编程实战

  ![FFMPEG编程实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_31.png "FFMPEG编程实战") 

### 1.4 流媒体实战

  ![流媒体实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_41.png "流媒体实战") 

### 1.5 WEBRTC实战

  ![WEBRTC实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_51.png "WEBRTC实战") 

<!--
### 1.1 音视频基础知识

#### 1.1.1 FFMPEG环境搭建

* Windows平台搭建FFMPEG
* Linux平台搭建FFMPEG

#### 1.1.2 音视频基础

* 音频基础
* 视频基础
* 常用工具
    * Medialnfo
    * VLC播放器

### 1.2 FFMPEG命令

* 视频录制
* 多媒体文件的分解/复用
* 裁剪与合并
* 图片/视频互转
* 直播相关
* 各种滤镜

### 1.3 FFMPEG编程实战

#### 1.3.1 音视频渲染

* SDL环境搭建
* SDL事件
* SDL线程
* YUV视频播放
* PCM声音播放

#### 1.3.2 FFmpeg API

* FFmpeg框架
* FFmpeg内存模型
* FFmpeg常用结构体

#### 1.3.3 音视频编码

* AAC编解码原理
* H264编解码原理
* AAC解码
* AAC编码
* H264解码
* H264编码
* FFmpeg解码流程
* FFmpeg编码流程

#### 1.3.4 音视频封装格式

* FLV封装格式
* MP4封装格式
* 多媒体解复用
* 多媒体复用实战
* 多媒体转封装格式实战

#### 1.3.5 音视频过滤器实战

* 音视频过滤器
* 视频过滤器

#### 1.3.6 播放器开发实战

* 播放器框架分析
* 模块划分
* 音视频解码
* 播放器控制
* 音视频同步

#### 1.3.7 ffplay播放器

* 掌握ffplay.c的意义
* ffplay框架分析
* 音视频解码
* 音视频控制
* 音视频同步
* 参数机制

#### 1.3.8 ffmpeg录制转码

* 掌握ffmpeg.c
* ffmpeg框架
* 音视频编码
* 封装格式转换
* 提取音频
* 提取视频
* logo叠加
* 音视频文件拼接
* filter机制

### 1.4 流媒体

#### 1.4.1 rtmp流媒体

* rtmp协议
* wireshark抓包
* rtmp拉流
* rtmp推流

#### 1.4.2 hls流媒体

* hls协议
* HTTP协议
* TS格式
* wireshark抓包
* hls拉流
* ffmpeg hls源码
* hls多码率机制

#### 1.4.3 http-flv流媒体

* http-flv协议
* wireshark抓包
* http chunk机制
* http-flv拉流
* ffmpeg http-flv源码

#### 1.4.4 RTMP/HLS/HTTP-FLV流媒体服务器

* 整体框架
* rtmp推流
* rtmp拉流
* hls拉流
* http-flv拉流
* FFmpeg转码
* 首屏秒开技术
* forward集群源码
* edge集群源码
* 负载均衡部署方式

#### 1.4.5 RTSP流媒体

* RTSP协议
* RTP协议
* RTCP协议
* RTSP流媒体服务器搭建
* RTSP推流
* RTSP拉流
* wireshark抓包
* RTSP流媒体服务器

### 1.5 WEBRTC

#### 1.5.1 WebRTC中级开发

* WebRTC通话原理
* WebRTC开发环境搭建
* coturn最佳搭建
* 如何采集音视频数据
* —对—通话时序
* 信令服务器设计
* Web一对一通话
* Web和Android通话
* AppRTC

#### 1.5.2 WebRTC高级开发

* 自定义摄像头分辨率
* 码率限制
* 调整编码器顺序
* Mesh模型多方通话
* Janus框架
* Janus Web客户端源码
* Janus Android客户端源码
* Janus Windows客户端源码
* Janus信令设计
* 基于Janus实现会议系统
* WebRTC源码编译
* 拥塞控制算法
* FEC
* jitter buffer

#### 1.5.3 Janus服务器源码

* 源码结构
* 插件机制
* 线程
* 信令交互过程
* videoroom
* sdp
* rtp
* srtp
* rtcp
* stun
* turn
-->

<h2 id="nav_2">🌐 开源框架</h2>

### 2.1 实时音视频开源项目

实时音视频应用共包括几个环节：采集、编码、前后处理、传输、解码、缓冲、渲染等很多环节。每一个细分环节，还有更细分的技术模块。<br/>
比如，前后处理环节有美颜、滤镜、回声消除、噪声抑制等，采集有麦克风阵列等，编解码有VP8、VP9、H.264、H.265等。
<br/>
采集->前处理编码->传输->解码后处理->渲染
<br/>
实时音视频开源项目思维导图
![音视频开源项目说明](https://www.0voice.com/uiwebsite/audio_video_streaming/02/audio_video_open_source_pro.png "音视频开源项目") 


##### 2.1.1 音视频编解码开源项目

<div align=left>
  
project|website|introduce
:------- | :--------------- | :------------
WebRTC|[webrtc.org](https://www.webrtc.org)|WebRTC实现了基于网页的视频会议，标准是WHATWG 协议，目的是通过浏览器提供简单的javascript就可以达到实时通讯（Real-Time Communications (RTC)）能力。WebRTC提供了视频会议的核心技术，包括音视频的采集、编解码、网络传输、显示等功能，并且还支持跨平台：windows，linux，mac，android。
x264|[www.linuxfromscratch.org](https://www.linuxfromscratch.org)|H.264是ITU（International Telecommunication Union，国际通信联盟）和MPEG（Motion Picture Experts Group，运动图像专家组）联合制定的视频编码标准。而x264是一个开源的H.264/MPEG-4 AVC视频编码函数库，是最好的有损视频编码器之一。
FFmpeg|[ffmpeg.org](https://www.ffmpeg.org)|FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。采用LGPL或GPL许可证。它提供了录制、转换以及流化音视频的完整解决方案。FFmpeg提供了编码、解码、转换、封装等功能，以及剪裁、缩放、色域等后期处理。
ijkplayer|[bilibili/ijkplayer](https://github.com/bilibili/ijkplayer)|ijkplayer 是一个基于 ffplay 的轻量级 Android/iOS 视频播放器。实现了跨平台功能，API易于集成；编译配置可裁剪，方便控制安装包大小；支持硬件加速解码，更加省电；提供Android平台下应用弹幕集成的解决方案。
JSMpeg|[jsmpeg.com](https://jsmpeg.com)|JSMpeg is a Video Player written in JavaScript. It consists of an MPEG-TS Demuxer, WebAssembly MPEG1 Video & MP2 Audio Decoders, WebGL & Canvas2D Renderers and WebAudio Sound Output. JSMpeg can load static files via Ajax and allows low latency streaming (~50ms) via WebSocktes.
Opus|[opus.nlpl.eu](https://opus.nlpl.eu)|Opus是一个有损声音编码的格式，由Xiph.Org基金会开发，之后由IETF（互联网工程任务组）进行标准化，目标是希望用单一格式包含声音和语音，取代Speex和Vorbis，且适用于网络上低延迟的即时声音传输，标准格式定义于RFC 6716文件。Opus格式是一个开放格式，使用上没有任何专利或限制。
live555|[www.live555.com](http://www.live555.com)|live555是一个为流媒体提供解决方案的跨平台的C++开源项目，它实现了标准流媒体传输，是一个为流媒体提供解决方案的跨平台的C++开源项目，它实现了对标准流媒体传输协议如RTP/RTCP、RTSP、SIP等的支持。Live555实现了对多种音视频编码格式的音视频数据的流化、接收和处理等支持，包括MPEG、H.263+ 、DV、JPEG视频和多种音频编码。

</div>

##### 2.1.2 服务端开源项目

project|website|introduce
:------- | :--------------- | :------------
jitsi|[webrtc.org](https://www.webrtc.org)|介绍
JsSIP|[webrtc.org](https://www.webrtc.org)|介绍
SRS|[webrtc.org](https://www.webrtc.org)|介绍
JRTPLIB|[webrtc.org](https://www.webrtc.org)|介绍
OPAL|[webrtc.org](https://www.webrtc.org)|介绍
Kurento|[webrtc.org](https://www.webrtc.org)|介绍
Janus|[webrtc.org](https://www.webrtc.org)|介绍

##### 2.1.3 质量传输服务端开源项目

project|website|introduce
:------- | :--------------- | :------------
callstats.io|[webrtc.org](https://www.webrtc.org)|介绍
Meetecho|[webrtc.org](https://www.webrtc.org)|介绍
Agora|[webrtc.org](https://www.webrtc.org)|介绍

##### 2.1.4 视频前后处理开源项目

###### 2.1.4.1 音频

project|website|introduce
:------- | :--------------- | :------------
soundtouch|[webrtc.org](https://www.webrtc.org)|介绍

###### 2.1.4.2 视频

project|website|introduce
:------- | :--------------- | :------------
seetaface|[webrtc.org](https://www.webrtc.org)|介绍
GPUlmage|[webrtc.org](https://www.webrtc.org)|介绍
open nsfw model|[webrtc.org](https://www.webrtc.org)|介绍

### 2.2 其他音视频开源项目

<div align=left>
  
project|website|introduce
:------- | :--------------- | :------------ 
Speex|[xiph.org](https://www.xiph.org)|Speex是一套主要针对语音的开源免费，无专利保护的音频压缩格式。
FLAC|[xiph.org](https://www.xiph.org)|FLAC中文可解释为无损音频压缩编码。FLAC是一套著名的自由音频压缩编码，其特点是无损压缩。不同于其他有损压缩编码如MP3及AAC，它不会破坏任何原有的音频信息，所以可以还原音乐光盘音质。
Xvid|[https://www.xvidmovies.com/players](https://www.xvidmovies.com/players/)|Xvid是一个开放源代码的MPEG-4视频编解码器，它是基于OpenDivX而编写的。
Lagarith|[lags.leetcode.net](https://lags.leetcode.net/index.htm)|Lagarith，是一种由Ben Greenwood所撰写的影片编解码器（video codec）。
Thor|[wwww.thor.com](https://www.thor.com)|Thor是思科开源的视频编码解码器，Thor拥有适当复杂度的高压缩率视频编码解码器，使用众所周知的 motion-compensated 预测的混合视频编码方法和变换编码。
  
</div>

<h2 id="nav_3">🧿 视频</h2>

<h2 id="nav_4">👀 业界大神</h2>

<h2 id="nav_5">🍀 paper</h2>

<h2 id="nav_6">🔰 书籍协议</h2>

<div align=left>

No.|Title|Translation（参考）|Company
:------- | :--------------- | :------------ | :-------
1|[《视频技术内幕》](https://github.com/0voice/computer_expert_paper/blob/main/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/%E3%80%8AA%20Declarative%20Query%20Language%20for%20Data%20Provenance%E3%80%8B.pdf)|《数据来源的声明式查询语言》|福斯-计算机科学研究所

</div>

<h2 id="nav_7">🍮 文章</h2>

<h2 id="nav_8">🥌 实践项目</h2>



