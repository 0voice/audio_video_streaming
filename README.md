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
  
|音视频开发     |开源框架      |视频          |业界大神      | paper        | 书籍协议       |  文章        |实践项目      |
| ------------ |:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|
|[🎵](#nav_1) | [🌐](#nav_2)|[🧿](#nav_3) |[👀](#nav_3)  |[🍀](#nav_4) |[🔰](#nav_5)    |[🍮](#nav_6) |[🥌](#nav_7)  |
| 更新完毕     | 整理中...    | 整理中...    | 整理中...    | 整理中...    | 整理中...      | 整理中...    | 整理中...    |
  
</div>

<br/>
<br/>
<br/>

<h2 id="nav_1">🎵 音视频开发</h2>

### 1.1 音视频基础知识入门

![音视频基础知识入门说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_1.png "音视频基础知识入门") 

### 1.2 FFMPEG命令实战

![FFMPEG命令实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_2.png "FFMPEG命令实战") 

### 1.3 FFMPEG编程实战

![FFMPEG编程实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_3.png "FFMPEG编程实战") 

### 1.4 流媒体实战

![流媒体实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_4.png "流媒体实战") 

### 1.5 WEBRTC实战

![WEBRTC实战说明](https://www.0voice.com/uiwebsite/audio_video_streaming/01/audio_video_5.png "WEBRTC实战") 

<!--
### 1.1 音视频基础知识入门

#### 1.1.1 FFMPEG环境搭建

* Windows平台搭建FFMPEG
* Linux平台搭建FFMPEG

#### 1.1.2 音视频基础入门

* 音频基础知识
* 视频基础知识
* 常用工具
    * Medialnfo
    * VLC播放器

### 1.2 FFMPEG命令实战

* 视频录制命令
* 多媒体文件的分解/复用命令
* 裁剪与合并命令
* 图片/视频互转命令
* 直播相关命令
* 各种滤镜命令

### 1.3 FFMPEG编程实战

#### 1.3.1 音视频渲染实战

* SDL环境搭建
* SDL事件处理
* SDL线程处理
* YUV视频播放实战
* PCM声音播放实战

#### 1.3.2 FFmpeg API精讲

* FFmpeg框架分析
* FFmpeg内存模型分析
* FFmpeg常用结构体精讲

#### 1.3.3 音视频编码实战

* AAC编解码原理
* H264编解码原理
* AAC解码实战
* AAC编码实战
* H264解码实战
* H264编码实战
* FFmpeg解码流程分析
* FFmpeg编码流程分析

#### 1.3.4 音视频封装格式实战

* FLV封装格式分析
* MP4封装格式分析
* 多媒体解复用实战
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

* 掌握ffmpeg.c的意义
* ffmpeg框架分析
* 音视频编码
* 封装格式转换
* 提取音频
* 提取视频
* logo叠加
* 音视频文件拼接
* filter机制

### 1.4 流媒体实战

#### 1.4.1 rtmp流媒体实战

* rtmp协议分析
* wireshark抓包分析
* rtmp拉流实战
* rtmp推流实战

#### 1.4.2 hls流媒体实战

* hls协议分析
* HTTP协议分析
* TS格式分析
* wireshark抓包分析
* hls拉流实战
* ffmpeg hls源码分析
* hls多码率机制

#### 1.4.3 http-flv流媒体实战

* http-flv协议分析
* wireshark抓包分析
* http chunk机制分析
* http-flv拉流实战
* ffmpeg http-flv源码分析

#### 1.4.4 RTMP/HLS/HTTP-FLV流媒体服务器分析

* 整体框架分析
* rtmp推流分析
* rtmp拉流分析
* hls拉流分析
* http-flv拉流分析
* FFmpeg转码分析
* 首屏秒开技术分析
* forward集群源码分析
* edge集群源码分析
* 负载均衡部署方式

#### 1.4.5 RTSP流媒体实战

* RTSP协议分析
* RTP协议分析
* RTCP协议分析
* RTSP流媒体服务器搭建
* RTSP推流实战
* RTSP拉流实战
* wireshark抓包分析
* RTSP流媒体服务器分析

### 1.5 WEBRTC实战

#### 1.5.1 WebRTC中级开发

* WebRTC通话原理分析
* WebRTC开发环境搭建
* coturn最佳搭建方法
* 如何采集音视频数据
* —对—通话时序分析
* 信令服务器设计
* Web一对一通话
* Web和Android通话
* AppRTC快速演示

#### 1.5.2 WebRTC高级开发

* 自定义摄像头分辨率
* 码率限制
* 调整编码器顺序
* Mesh模型多方通话
* Janus框架分析
* Janus Web客户端源码分析
* Janus Android客户端源码分析
* Janus Windows客户端源码分析
* Janus信令设计
* 基于Janus实现会议系统
* WebRTC源码编译
* 拥塞控制算法
* FEC
* jitter buffer

#### 1.5.3 Janus服务器源码分析

* 源码结构
* 插件机制
* 线程分析
* 信令交互过程
* videoroom分析
* sdp分析
* rtp分析
* srtp分析
* rtcp分析
* stun分析
* turn分析
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


##### 2.1.1 音视频编解码类

<div align=left>
  
project|website|introduce
:-------: | :---------------: | :------------: 
WebRTC|[webrtc.org](www.webrtc.org)|首先会用到的肯定是WebRTC，是一个支持网页浏览器进行实时语音对话或视频对话的开源项目。它提供了包括音视频的采集、编解码、网络传输、显示等功能。如果你想基于WebRTC开发实时音视频应用，需要注意，由于WebRTC缺少服务端设计和部署方案，还需要将WebRTC与Janus等服务端类开源项目结合即可。
x264|[www.videolan.org/developers](www.videolan.org/developers)|H.264是目前应用最广的码流标准。x264则是能够产生符合H.264标准的码流的编码器，它可以将视频流编码为H.264、MPEG-4 AVC格式。它提供了命令行接口与API，前者被用于一些图形用户接口例如Straxrip、MeGUI，后者则被FFmpeg、Handbrake等调用。当然，既然有x264，就有对应HEVC/H.265的x265。
FFmpeg|[ffmpeg.org](www.ffmpeg.org)|FFmpeg提供了编码、解码、转换、封装等功能，以及剪裁、缩放、色域等后期处理，支持几乎目前所有音视频编码标准（由于格式众多，可以在Wikipedia中找到）。

</div>


##### 2.1.2 视频前后处理

##### 2.1.3 服务端类等

### 2.2 音视频开源项目

实时音视频开发中会用到开源项目

<h2 id="nav_3">🧿 视频</h2>

<h2 id="nav_4">👀 业界大神</h2>

<h2 id="nav_5">🍀 paper</h2>

<h2 id="nav_6">🔰 书籍协议</h2>

<div align=center>

No.|Title|Translation（参考）|Company
:-------: | :---------------: | :------------: | :-------:
1|[《A Declarative Query Language for Data Provenance》](https://github.com/0voice/computer_expert_paper/blob/main/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/%E3%80%8AA%20Declarative%20Query%20Language%20for%20Data%20Provenance%E3%80%8B.pdf)|《数据来源的声明式查询语言》|福斯-计算机科学研究所

</div>

<h2 id="nav_7">🍮 文章</h2>

<h2 id="nav_8">🥌 实践项目</h2>



