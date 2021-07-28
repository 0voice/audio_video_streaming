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
  
音视频开发     |开源框架      |视频          |业界大神      | paper        | 书籍          |  面试题      |文章          |协议          |实践项目      
:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:
[🎵](#nav_1) | [🌐](#nav_2)|[🧿](#nav_3) |[👀](#nav_4)  |[🍀](#nav_5)  |[📙](#nav_6)   |[🌅](#nav_7) |[📰](#nav_8) |[🧾](#nav_9)  |[🥌](#nav_10)  
  
</div>

<br/>
<br/>
<br/>

<h2 id="nav_1">🎵 音视频开发</h2>

### 1.1 音视频基础

>FFMPEG环境搭建
>  * Windows平台搭建FFMPEG
>  * Linux平台搭建FFMPEG

>音视频基础
>  * 音频基础
>  * 视频基础
>  * 常用工具
>     * Medialnfo
>     * VLC播放器

### 1.2 FFMPEG命令

>  * 视频录制
>  * 多媒体文件的分解/复用
>  * 裁剪与合并
>  * 图片/视频互转
>  * 直播相关
>  * 各种滤镜

### 1.3 FFMPEG编程

>音视频渲染
>  * SDL环境搭建
>  * SDL事件
>  * SDL线程
>  * YUV视频播放
>  * PCM声音播放

>FFmpeg API
>  * FFmpeg框架
>  * FFmpeg内存模型
>  * FFmpeg常用结构体

>音视频编码
>  * AAC编解码原理
>  * H264编解码原理
>  * AAC解码
>  * AAC编码
>  * H264解码
>  * H264编码
>  * FFmpeg解码流程
>  * FFmpeg编码流程

>音视频封装格式
>  * FLV封装格式
>  * MP4封装格式
>  * 多媒体解复用
>  * 多媒体复用实战
>  * 多媒体转封装格式实战

>音视频过滤器
>  * 音视频过滤器
>  * 视频过滤器

>播放器开发
>  * 播放器框架
>  * 模块
>  * 音视频解码
>  * 播放器控制
>  * 音视频同步

>ffplay播放器
>  * 掌握ffplay.c的意义
>  * ffplay框架
>  * 音视频解码
>  * 音视频控制
>  * 音视频同步
>  * 参数机制

>ffmpeg录制转码
>  * 掌握ffmpeg.c
>  * ffmpeg框架
>  * 音视频编码
>  * 封装格式转换
>  * 提取音频
>  * 提取视频
>  * logo叠加
>  * 音视频文件拼接
>  * filter机制

### 1.4 流媒体

>rtmp流媒体
>  * rtmp
>  * wireshark抓包
>  * rtmp拉流
>  * rtmp推流

>hls流媒体
>  * hls
>  * HTTP
>  * TS格式
>  * wireshark
>  * hls拉流
>  * ffmpeg hls源码
>  * hls多码率机制

>http-flv流媒体
>  * http-flv
>  * wireshark
>  * http chunk机制
>  * http-flv拉流
>  * ffmpeg http-flv源码

>RTMP/HLS/HTTP-FLV流媒体服务器
>  * 整体框架
>  * rtmp推流
>  * rtmp拉流
>  * hls拉流
>  * http-flv拉流
>  * FFmpeg转码
>  * 首屏秒开技术
>  * forward集群源码
>  * edge集群源码
>  * 负载均衡部署方式

>RTSP流媒体
>  * RTSP
>  * RTP
>  * RTCP
>  * RTSP流媒体服务器搭建
>  * RTSP推流
>  * RTSP拉流
>  * wireshark
>  * RTSP流媒体服务器

### 1.5 WEBRTC

>WebRTC中级开发
>  * WebRTC通话原理
>  * WebRTC开发环境搭建
>  * coturn最佳搭建
>  * 如何采集音视频数据
>  * —对—通话时序
>  * 信令服务器设计
>  * Web一对一通话
>  * Web和Android通话
>  * AppRTC

>WebRTC高级开发
>  * 自定义摄像头分辨率
>  * 码率限制
>  * 调整编码器顺序
>  * Mesh模型多方通话
>  * Janus框架
>  * Janus Web客户端源码
>  * Janus Android客户端源码
>  * Janus Windows客户端源码
>  * Janus信令设计
>  * 基于Janus实现会议系统
>  * WebRTC源码编译
>  * 拥塞控制算法
>  * FEC
>  * jitter buffer

>Janus服务器源码
>  * 源码结构
>  * 插件机制
>  * 线程
>  * 信令交互过程
>  * videoroom
>  * sdp
>  * rtp
>  * srtp
>  * rtcp
>  * stun
>  * turn

<br/>
<br/>

<h2 id="nav_2">🌐 开源框架</h2>

### 2.1 实时音视频开源项目

实时音视频应用共包括几个环节：采集、编码、前后处理、传输、解码、缓冲、渲染等很多环节。每一个细分环节，还有更细分的技术模块。<br/>
比如，前后处理环节有美颜、滤镜、回声消除、噪声抑制等，采集有麦克风阵列等，编解码有VP8、VP9、H.264、H.265等。
<br/>
采集->前处理编码->传输->解码后处理->渲染
<br/>

实时音视频开源项目思维导图
![音视频开源项目说明](https://www.0voice.com/uiwebsite/audio_video_streaming/02/audio_video_open_source_pro.png "音视频开源项目") 

##### 2.1.1 编解码开源项目
  
project|website|introduce
:------- | :--------------- | :------------
WebRTC|[webrtc.org](https://www.webrtc.org)|WebRTC实现了基于网页的视频会议，标准是WHATWG 协议，目的是通过浏览器提供简单的javascript就可以达到实时通讯（Real-Time Communications (RTC)）能力。WebRTC提供了视频会议的核心技术，包括音视频的采集、编解码、网络传输、显示等功能，并且还支持跨平台：windows，linux，mac，android。
x264|[www.linuxfromscratch.org](https://www.linuxfromscratch.org)|H.264是ITU（International Telecommunication Union，国际通信联盟）和MPEG（Motion Picture Experts Group，运动图像专家组）联合制定的视频编码标准。而x264是一个开源的H.264/MPEG-4 AVC视频编码函数库，是最好的有损视频编码器之一。
FFmpeg|[ffmpeg.org](https://www.ffmpeg.org)|FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。采用LGPL或GPL许可证。它提供了录制、转换以及流化音视频的完整解决方案。FFmpeg提供了编码、解码、转换、封装等功能，以及剪裁、缩放、色域等后期处理。
ijkplayer|[bilibili/ijkplayer](https://github.com/bilibili/ijkplayer)|ijkplayer 是一个基于 ffplay 的轻量级 Android/iOS 视频播放器。实现了跨平台功能，API易于集成；编译配置可裁剪，方便控制安装包大小；支持硬件加速解码，更加省电；提供Android平台下应用弹幕集成的解决方案。
JSMpeg|[jsmpeg.com](https://jsmpeg.com)|JSMpeg is a Video Player written in JavaScript. It consists of an MPEG-TS Demuxer, WebAssembly MPEG1 Video & MP2 Audio Decoders, WebGL & Canvas2D Renderers and WebAudio Sound Output. JSMpeg can load static files via Ajax and allows low latency streaming (~50ms) via WebSocktes.
Opus|[opus.nlpl.eu](https://opus.nlpl.eu)|Opus是一个有损声音编码的格式，由Xiph.Org基金会开发，之后由IETF（互联网工程任务组）进行标准化，目标是希望用单一格式包含声音和语音，取代Speex和Vorbis，且适用于网络上低延迟的即时声音传输，标准格式定义于RFC 6716文件。Opus格式是一个开放格式，使用上没有任何专利或限制。
live555|[www.live555.com](http://www.live555.com)|live555是一个为流媒体提供解决方案的跨平台的C++开源项目，它实现了标准流媒体传输，是一个为流媒体提供解决方案的跨平台的C++开源项目，它实现了对标准流媒体传输协议如RTP/RTCP、RTSP、SIP等的支持。Live555实现了对多种音视频编码格式的音视频数据的流化、接收和处理等支持，包括MPEG、H.263+ 、DV、JPEG视频和多种音频编码。


##### 2.1.2 服务端开源项目

project|website|introduce
:------- | :--------------- | :------------
jitsi|[jitsi/jitsi](https://github.com/jitsi/jitsi)|Jitsi is an audio/video and chat communicator that supports protocols such as SIP, XMPP/Jabber, IRC and many other useful features.
JsSIP|[jssip.net](https://jssip.net)|JsSIP是一个简单易用的JavaScript库，它利用SIP和WebRTC的最新发展，在任何网站上提供全功能的SIP端点。通过JsSIP ，只要几行代码，任何网站都可以通过音频，视频等获得实时通信功能。
SRS|[www.ossrs.net](http://www.ossrs.net)|SRS定位是运营级的互联网直播服务器集群，追求更好的概念完整性和最简单实现的代码。SRS提供了丰富的接入方案将RTMP流接入SRS，包括推送RTMP到SRS、推送RTSP/UDP/FLV到SRS、拉取流到SRS。SRS还支持将接入的RTMP流进行各种变换，譬如将RTMP流转码、流截图、转发给其他服务器、转封装成HTTP-FLV流、转封装成HLS、转封装成HDS、录制成FLV。SRS包含支大规模集群如CDN业务的关键特性，譬如RTMP多级集群、源站集群、VHOST虚拟服务器、无中断服务Reload、HTTP-FLV集群、Kafka对接。此外，SRS还提供丰富的应用接口，包括HTTP回调、安全策略Security、HTTP API接口、RTMP测速。
JRTPLIB|[j0r1/JRTPLIB](https://github.com/j0r1/JRTPLIB)|jrtplib是一个基于C++、面向对象的RTP封装库, jrtplib支持定义于RFC3550中的RTP协议，它使得发送和接收RTP报文变得异常简单，用户不用担心SSRC冲突，也不用考虑如何传输RTCP数据，因为RTCP功能完全在内部实现。
OPAL|[opalvoip](http://sourceforge.net/projects/opalvoip/files/)|Open Phone Abstraction Library (OPAL) is a C++ multi-platform, multi-protocol library for Fax, Video & Voice over IP and other networks. Also included is the Portable Tool Library (PTLib) which is a C++ multi-platform abstraction library.
Kurento|[www.kurento.org](http://www.kurento.org)|Kurento 是一个WebRTC流媒体服务器以及一些客户端API，开发WWW及智能手机平台的高级视频应用就变得更加容易。可以利用Kurento开发的应用类型包括，视频会议，音视频广播，音视频录制、转码等。
Janus|[janus.conf.meetecho.com](https://janus.conf.meetecho.com)|Janus 是由Meetecho设计和开发的开源、通用的基于SFU架构的WebRTC流媒体服务器，它支持在Linux的服务器或MacOS上的机器进行编译和安装。

##### 2.1.3 质量传输开源项目

project|website|introduce
:------- | :--------------- | :------------
callstats.io|[callstats](https://www.callstats.io)|Callstats.io致力于监控和管理WebRTC应用中的音频和视频通话性能。提供Javascript客户端库，可以监测浏览器终端性能，从而帮助服务供应商准确定位那些媒体质量较低的终端用户，并进行性能问题的诊断。该信息主要是用于产品经理和工程师来提高客户体验质量，主动解决潜在的瓶颈障碍。
Meetecho|[meetecho/janus-gateway](https://github.com/meetecho/janus-gateway)|Meetecho Janus是Meetecho公司的一款WebRTC（网页即时通信）服务器。
Agora|[agora.io](https://www.agora.io/cn)|声网Agora提供了一套简单而强大的SDK,开发者可以利用其中的资源在任何手机或电脑应用中加入高清语音和视频通讯功能。

##### 2.1.4 视频前后处理开源项目

###### 2.1.4.1 音频

project|website|introduce
:------- | :--------------- | :------------
soundtouch|[soundtouch](https://gitlab.com/soundtouch/soundtouch)|SoundTouch是一个开源的音频处理库，主要实现包含变速、变调、变速同时变调等三个 功能模块，能够对媒体流实时操作，也能对音频文件操作。采用32位浮点或者16位定点，支持单声道或者双声道，采样率范围为8k~48k。

###### 2.1.4.2 视频

project|website|introduce
:------- | :--------------- | :------------
SeetaFace6|[SeetaFace6Open](https://github.com/SeetaFace6Open/index)|SeetaFace6是中科视拓最新开源的商业正式版本。包含人脸识别的基本部分，如人脸检测、关键点定位、人脸识别。同时增加了活体检测、质量评估、年龄性别估计。并且响应时事，开放了口罩检测以及戴口罩的人脸识别模型。
GPUImage2|[GPUImage2](https://github.com/BradLarson/GPUImage2)|GPUImage是个功能十分强大、又十分易用的图像处理库。提供各种各样的图像处理滤镜，并且支持照相机和摄像机的实时滤镜。
open nsfw|[open_nsfw](https://github.com/yahoo/open_nsfw)|open nsfw是雅虎开源项目caffeonspark，使用深度学习训练得到caffe模型。nsfw翻译为不可在工作中看的图片。主要是针对黄图的，恐怖，血腥图片不能识别。

### 2.2 其他音视频开源项目
  
project|website|introduce
:------- | :--------------- | :------------ 
Speex|[xiph.org](https://www.xiph.org)|Speex是一套主要针对语音的开源免费，无专利保护的音频压缩格式。
FLAC|[xiph.org](https://www.xiph.org)|FLAC中文可解释为无损音频压缩编码。FLAC是一套著名的自由音频压缩编码，其特点是无损压缩。不同于其他有损压缩编码如MP3及AAC，它不会破坏任何原有的音频信息，所以可以还原音乐光盘音质。
Xvid|[xvidmovies](https://www.xvidmovies.com/players/)|Xvid是一个开放源代码的MPEG-4视频编解码器，它是基于OpenDivX而编写的。
Lagarith|[lags.leetcode.net](https://lags.leetcode.net/index.htm)|Lagarith，是一种由Ben Greenwood所撰写的影片编解码器（video codec）。
Thor|[wwww.thor.com](https://www.thor.com)|Thor是思科开源的视频编码解码器，Thor拥有适当复杂度的高压缩率视频编码解码器，使用众所周知的 motion-compensated 预测的混合视频编码方法和变换编码。
  
<br/>
<br/>

<h2 id="nav_3">🧿 视频</h2>

<br/>
<br/>

<h2 id="nav_4">👀 业界大神</h2>

No.|author|introduce
:------- | :--------------- | :------------ 
1|刘岐|FFmpeg官方代码维护者之一，十余年一线技术研发与技术管理经验，人称“大师兄”。现任职于OnVideo公司，担任CTO，公司联合创立人，负责在线音视频云编辑与创作平台的开发和建设。曾任职蓝汛、高升、金山云等公司，担任视频部门架构师及技术专家。
2|赵文杰|擅长音视频编解码和渲染技术，客户端技术专家，开源流媒体服务器SRS开发者之一，现任好未来网校事业部高级架构师一职，负责端开发。
3|廖庆富|主要从事音视频驱动，多媒体中间件，流媒体服务器的高级开发，主导开发过即时通讯+音视频通话的大型项目。曾就职于联发科，现任职于零声教育，资深音视频讲师。主讲WebRTC,ffmpeg,流媒体。[博客主页](https://www.zhihu.com/people/liao-ming-hua)

<br/>
<br/>

<h2 id="nav_5">🍀 paper</h2>

<br/>
<br/>

<h2 id="nav_6">📙 书籍</h2>

### 6.1 音频

No.|book name|author|introduction
:------- | :--------------- | :------------ | :-------
1|《WebRTC技术详解：从0到1构建多人视频会议系统》|栗伟|全面讲解WebRTC各项技术，案例代码可直接用于视频会议、在线教育场景，开源商用视频会议系统。
2|《音视频开发进阶指南：基于Android与iOS平台的实践》|展晓凯 魏晓红|书中介绍音视频的物理现象与基础概念，帮助读者建立模拟信号到数字信号转化的过程，然后重点介绍了如何在移动端开发音视频项目，其中包括开发中所需要了解的各种知识，如音视频的解码与渲染，采集与编码，音视频的处理与性能优化等。
3|《Android音视频开发》|何俊林 |本书着重介绍音视频基础知识、MediaPlayer、MediaPlayerService、StagefrightPlayer、NuPlayer、OpenMAX框架、FFmpeg项目、FFmpeg源码分析及实战、直播技术、H.264编码及H.265编码、视频格式分析内容。
4|《FFmpeg从入门到精通》|刘歧 |本书围绕着音视频处理的FFmpeg的发展过程、FFmpeg的组成、FFmpeg的命令行使用、FFmpeg的API使用等内容，由浅入深地介绍了使用FFmpeg进行音视频处理的方法，并辅以大量实例，从而帮助对音视频处理感兴趣的读者对FFmpeg有更多的了解。

### 6.2 视频
No.|book name|author|introduction
:------- | :--------------- | :------------ | :-------
1|《在线视频技术精要》|晓成 |本书着重介绍在线视频行业的基础——音视频技术，从行业的历史、文件格式、标准组织开始，依次介绍了音视频技术的框架、编码、流媒体、播放等知识。

<br/>
<br/>

<h2 id="nav_7">🌅 面试题</h2>

##### [1. 为什么巨大的原始视频可以编码成很小的视频呢?这其中的技术是什么呢?](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_001)
##### [2. 怎么做到直播秒开优化？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_002)
##### [3. 直方图在图像处理里面最重要的作用是什么？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_003)
##### [4. 数字图像滤波有哪些方法？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_004)
##### [5. 图像可以提取的特征有哪些？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_005)
##### [6. 衡量图像重建好坏的标准有哪些？怎样计算？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_006)
##### [7. AAC和PCM的区别？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_007)
##### [8. H264存储的两个形态？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_008)
##### [9. FFMPEG：图片如何合成视频？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_009)
##### [10. 常见的音视频格式有哪些？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_010)
##### [11. 请指出“1080p”的意义？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_011)
##### [12. 请解释颜色的本质及其数字记录原理，并说出几个你所知道的色域。](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_012)
##### [13. 请解释“矢量图”和“位图”的区别？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_013)
##### [14. 请从“光圈”“快门速度”“感光度”“白平衡”“景深”中任选2个进行叙述？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_014)
##### [15. 视频分量YUV的意义及数字化格式？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_015)
##### [16. 在MPEG标准中图像类型有哪些？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_016)
##### [17. 列举一些音频编解码常用的实现方案？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_017)
##### [18. 请叙述MPEG视频基本码流结构？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_018)
##### [19. sps和pps的区别？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_019)
##### [20. 请叙述AMR基本码流结构？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_020)
##### [21. 预测编码的基本原理是什么？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_021)
##### [22. 说一说ffmpeg的数据结构？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_022)
##### [23. 说一说AVFormatContext 和 AVInputFormat之间的关系？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_023)
##### [24. 说一说AVFormatContext, AVStream和AVCodecContext之间的关系？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_024)
##### [25. 说一说视频拼接处理步骤？（细节处理，比如分辨率大小不一，时间处理等等）](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_025)
##### [26. NV21如何转换成I420？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_026)
##### [27. DTS与PTS共同点？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_027)
##### [28. 影响视频清晰度的指标有哪些？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_028)
##### [29. 编解码处理时遇到什么困难？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_029)
##### [30. 如何秒开视频？什么是秒开视频？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_030)
##### [31. 如何降低延迟？如何保证流畅性？如何解决卡顿？解决网络抖动？](https://github.com/0voice/audio_video_streaming/blob/main/case_interview/001-README.md#subject_031)
<br/>
<br/>

<h2 id="nav_8">📰 文章</h2>
<!--
No.|article
:------- | :--------------- 
1| [WebRTC 发送方码率预估实现解析](https://github.com/0voice/audio_video_streaming/blob/main/article/001.REMARD.md)
-->

No.|article
:------- | :--------------- 
1|[WebRTC 发送方码率预估实现解析](https://github.com/0voice/audio_video_streaming/blob/main/article/001-WebRTC%20发送方码率预估实现解析.md)
2|[码率控制基本概念](https://github.com/0voice/audio_video_streaming/blob/main/article/002-码率控制基本概念.md)
3|[Speex回声消除代码分析](https://github.com/0voice/audio_video_streaming/blob/main/article/003-Speex回声消除代码分析.md)
4|[房间声学原理与Schroeder混响算法实现](https://github.com/0voice/audio_video_streaming/blob/main/article/004-房间声学原理与Schroeder混响算法实现.md)
5|[H264系列--压缩编码技术](https://github.com/0voice/audio_video_streaming/blob/main/article/005-H264系列--压缩编码技术.md)
6|[RTSP 媒体协议流的录制方案及其覆盖策略详解](https://github.com/0voice/audio_video_streaming/blob/main/article/006-RTSP%20媒体协议流的录制方案及其覆盖策略详解.md)
7|[建立连接之ICE框架](https://github.com/0voice/audio_video_streaming/blob/main/article/007-webrtc建立连接之ICE框架.md)
8|[流媒体协议介绍（rtp/rtcp/rtsp/rtmp/mms/hls）](https://github.com/0voice/audio_video_streaming/blob/main/article/008-流媒体协议介绍.md)
9|[音视频同步原理及实现](https://github.com/0voice/audio_video_streaming/blob/main/article/009-音视频同步原理及实现.md)
10|[直播概念和流程框架](https://github.com/0voice/audio_video_streaming/blob/main/article/010-直播概念和流程框架.md)
11|[CDN在直播中的运用](https://github.com/0voice/audio_video_streaming/blob/main/article/011-CDN在直播中的运用.md)
12|[常见音视频编码格式](https://github.com/0voice/audio_video_streaming/blob/main/article/012-常见音视频编码格式.md)
13|[H.264官方软件JM源代码分析-编码器lencod](https://github.com/0voice/audio_video_streaming/blob/main/article/013-H.264官方软件JM源代码分析-编码器lencod.md)
14|[H.264官方软件JM源代码分析-解码器ldecod](https://github.com/0voice/audio_video_streaming/blob/main/article/014-H.264官方软件JM源代码分析-解码器ldecod.md)
15|[Android 音视频技术](https://github.com/0voice/audio_video_streaming/blob/main/article/015-Android%20音视频技术.md)
16|[Web前端WebRTC攻略-媒体协商与SDP简析](https://github.com/0voice/audio_video_streaming/blob/main/article/016-Web前端WebRTC攻略-媒体协商与SDP简析.md)
17|[基于FFmpeg的AVfilter的例子-纯净版](https://github.com/0voice/audio_video_streaming/blob/main/article/017-基于FFmpeg的AVfilter的例子-纯净版.md)
18|[WebRTC 传输安全机制第二话：深入显出 SRTP 协议](https://github.com/0voice/audio_video_streaming/blob/main/article/018-WebRTC%20传输安全机制第二话：深入显出%20SRTP%20协议.md)
19|[WebRTC能给我带来什么？](https://github.com/0voice/audio_video_streaming/blob/main/article/019-WebRTC能给我带来什么？.md)
20|[视音频数据处理：RGB、YUV像素数据处理](https://github.com/0voice/audio_video_streaming/blob/main/article/020-视音频数据处理：RGB、YUV像素数据处理.md)
21|[视音频数据处理：PCM音频采样数据处理](https://github.com/0voice/audio_video_streaming/blob/main/article/021-视音频数据处理：PCM音频采样数据处理.md)
22|[视音频数据处理：H.264视频码流解析](https://github.com/0voice/audio_video_streaming/blob/main/article/022-视音频数据处理：H.264视频码流解析.md)
23|[视音频数据处理：AAC音频码流解析](https://github.com/0voice/audio_video_streaming/blob/main/article/023-视音频数据处理：AAC音频码流解析.md)
24|[视音频数据处理：FLV封装格式解析](https://github.com/0voice/audio_video_streaming/blob/main/article/024-视音频数据处理：FLV封装格式解析.md)
25|[视音频数据处理：UDP-RTP协议解析](https://github.com/0voice/audio_video_streaming/blob/main/article/025-视音频数据处理：UDP-RTP协议解析.md)

<br/>
<br/>

<h2 id="nav_9">🧾 协议</h2>

* HTTP
* RTP
* RTSP
* RealMedia RTSP/RDT
* TCP
* UDP
* Gopher
* RTMP
* RTMPT, RTMPE, RTMPTE, RTMPS (via librtmp)
* SDP
* MMS over TCP

<br/>
<br/>

<h2 id="nav_10">🥌 实践项目</h2>

### 10.1 音视频通话项目

实现了多人语音/多人视频项目，房间号Id一致，用户Id不一致，即可实现多人语音/多人视频。 [项目地址](https://webrtc.0voice.com)
