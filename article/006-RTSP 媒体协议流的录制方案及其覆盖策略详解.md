# RTSP 媒体协议流的录制方案及其覆盖策略详解

## 前言

在安防和监控领域，RTSP 媒体协议流有很广泛的使用。本文将介绍一种针对 RTSP 媒体流的录制方案及其相应的覆盖策略。据我所知，声网的实时录制功能支持三种模式，分别是云端录制、本地服务端录制和页面录制，今天我们介绍的录制方案和声网的云端录制类似。

## 正文

本文将从录制视频格式的调研、录制方案的选择、异常状况的处理、覆盖策略的执行四个大方面进行介绍。

### 1. 录制视频格式调研
如果想要实现 RTSP 媒体流的录制功能，就需要考虑录制目标文件的格式，也就是把媒体流录制成哪种格式的视频文件。起初我们预设了三种方案，经过一系列调研后，最终选择了 m3u8。接下来，我们简单介绍一下这个选择过程。

### 1.1 为什么不用 mp4 格式
mp4 是点播视频中最为常见的视频格式，综合分析下来并不符合我们的使用场景。一般情况下，一个电影视频的最大时长也就两到三个小时左右，保存成一个 mp4 文件就够用了，但是在安防和监控场景下，一个摄像头对应的录制视频文件的长度可能是十几个小时，甚至是十几天。所以，对比下来，mp4 格式更适用于电影网站。

这就引出了 mp4 格式的一个缺点，如果录制存储为一个 mp4 格式，那文件体积可能会非常大。那么，存储的时候就会面临一系列问题，比如磁盘空间不足、大文件分片等状况的处理，特别是录制过程中数据流异常中断可能会导致已经录制的 mp4 文件不可用，这是其一。

![image](https://user-images.githubusercontent.com/87458342/127258635-5f46367d-75bd-4c31-bc59-9adb1365f011.png)

我们知道 mp4 文件是由许多 Box 和 FullBox 组成的，可以参考上图的 Box 树形图，其中，FullBox 是 Box 的扩展，每个 Box 又包含 Header 和 Data 两部分，moov Box 记录了整个 mp4 文件的音视频媒体信息。而 moov Box 一般是在 mp4 文件写完时才在文件尾部添加。因此，又引出了另外一个缺点，如果 mp4 文件特别大，那么在播放的时候，播放器需要加载全部的视频文件到内存中，如果视频文件特别大，这几乎是不现实的。因此，我们在录制结束保存 mp4 的时候，需要把 moov Box 调整到文件头部来避免这个问题。

### 1.2 为什么不用 mpd 格式
mpd 格式类似于 m3u8 格式，但是它采用的是 XML 的组织形式。我们不选择它的原因也有两个，其一，mpd 格式在现有产品线上没有类似使用场景，我们使用更多的是 m3u8，换句话说就是技术储备不足。

其二，播放器方案的通用性上存在问题，如果使用 mpd 格式，那么我们的播放器方案需要调整，能够支持 mpd 格式媒体的播放，这样一来会给播放器带来一定的工作量和隐含的问题。

最后，给出一个 mpd 的文件示例，让大家对其有一个更加直观的了解。

```HTML
<?xml version="1.0" encoding="UTF-8"?>
<!--Generated with https://github.com/google/shaka-packager version 97fc982-release-->
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xlink="http://www.w3.org/1999/xlink" xsi:schemaLocation="urn:mpeg:dash:schema:mpd:2011 DASH-MPD.xsd" xmlns:cenc="urn:mpeg:cenc:2013" minBufferTime="PT2S" type="static" profiles="urn:mpeg:dash:profile:isoff-on-demand:2011" mediaPresentationDuration="PT734S">
  <Period id="0">
    <AdaptationSet id="0" contentType="audio" lang="en">
      <Representation id="0" bandwidth="131596" codecs="mp4a.40.2" mimeType="audio/mp4" audioSamplingRate="44100">
        <AudioChannelConfiguration schemeIdUri="urn:mpeg:dash:23003:3:audio_channel_configuration:2011" value="2"/>
        <BaseURL>tears_audio_eng.mp4</BaseURL>
        <SegmentBase indexRange="745-1664" timescale="44100">
          <Initialization range="0-744"/>
        </SegmentBase>
      </Representation>
    </AdaptationSet>
    <AdaptationSet id="1" contentType="video" maxWidth="1920" maxHeight="856" frameRate="12288/512" par="38:17">
      <Representation id="1" bandwidth="769255" codecs="avc1.42c01e" mimeType="video/mp4" sar="852:857" width="320" height="142">
        <BaseURL>tears_h264_baseline_240p_800.mp4</BaseURL>
        <SegmentBase indexRange="827-1602" timescale="12288">
          <Initialization range="0-826"/>
        </SegmentBase>
      </Representation>
      <Representation id="2" bandwidth="1774254" codecs="avc1.4d401f" mimeType="video/mp4" sar="2242:2249" width="854" height="380">
        <BaseURL>tears_h264_main_480p_2000.mp4</BaseURL>
        <SegmentBase indexRange="829-1604" timescale="12288">
          <Initialization range="0-828"/>
        </SegmentBase>
      </Representation>
      <Representation id="3" bandwidth="7203938" codecs="avc1.4d4028" mimeType="video/mp4" sar="855:857" width="1280" height="570">
        <BaseURL>tears_h264_main_720p_8000.mp4</BaseURL>
        <SegmentBase indexRange="830-1605" timescale="12288">
          <Initialization range="0-829"/>
        </SegmentBase>
      </Representation>
      <Representation id="4" bandwidth="18316946" codecs="avc1.64002a" mimeType="video/mp4" sar="856:857" width="1920" height="856">
        <BaseURL>tears_h264_high_1080p_20000.mp4</BaseURL>
        <SegmentBase indexRange="832-1607" timescale="12288">
          <Initialization range="0-831"/>
        </SegmentBase>
      </Representation>
    </AdaptationSet>
  </Period>
</MPD>
```

通过上述文件，我们可以知道这个 mpd 文件包含了一路音频流，同时支持三种不同分辨率和码率的视频流。不同的媒体类型是用 AdaptationSet 标签表示的，内部还可以使用 Representation 标签标记不同分辨率和码率的媒体流。

### 1.3 为什么最终选择 m3u8 格式
选择 m3u8 的话，优势就会更加明显，除了规避上述方案的问题外，还有一些自身的优势，具体表现如下：

1. 本身就是 ts 分片存储形式，不需要再单独考虑大文件的切片问题。
2. 现有播放器方案支持 m3u8 格式，不需要再单独进行适配。
3. 具有一定的技术储备，开发上手快，开发周期可控。
4. 相应的覆盖策略执行起来会更加方便。

最后，给出一个 m3u8 的文件示例，让大家对其有一个更加直观的了解。

```C++
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:17
#EXT-X-MEDIA-SEQUENCE:0
#EXTINF:11.933333,
index_0000.ts
#EXTINF:3.866667,
index_0001.ts
#EXTINF:7.333333,
index_0002.ts
#EXTINF:16.666667,
index_0003.ts
#EXTINF:4.133333,
index_0004.ts
#EXT-X-ENDLIST
```

通过上述文件，我们可以知道这个 m3u8 文件包含了 5 个 ts 分片，以及它们各自的时长信息。文件以 #EXTM3U 标签开始，并以 #EXT-X-ENDLIST 标签结束。这里有一点需要注意，如果是直播使用的 m3u8 文件，它是没有 #EXT-X-ENDLIST 标签的。

## 2. 录制方案选择
既然已经确定了目标文件的格式，那么我们就要考虑怎么实现了。目前有两个方案可以考虑，一个是 Golang 纯原生方案，另一个是利用 ffmpeg 实现，接下来分别介绍。

### 2.1 Go 原生
利用纯原生的 Golang 实现，其实，Golang 处理音视频数据还是有一定优势的，通过解封装 RTSP 媒体流，得到音频数据和视频数据，然后创建对应的解码器，得到对应的原始音频 PCM 数据和原始视频 YUV 数据，再分别编码成 AAC 的音频和 H264 的视频，最后保存成 m3u8 格式的录制文件。整个过程可以参考下图：

![image](https://user-images.githubusercontent.com/87458342/127258781-d55e8500-5f10-4a10-ac09-c1b368e910df.png)

这种方案，编码的工作量会稍微大一些，同时有很多音视频数据处理的细节问题，负载度和难易程度上不如 ffmpeg 方案。

### 2.2 ffmpeg
利用 ffmpeg 工具库，通过启用 ffmpeg 进程来完成对应的 RTSP 流数据接收和 m3u8 文件录制保存工作，这样会更加简单，我们只需要管理好进程的创建、释放和异常处理工作。

## 3. 异常处理

录制过程中会遇到各种各样的问题，接下会分别介绍。有一点是相同的，所有的异常状况都会通知到录制调度服务，由调度服务进行统一分析和管理，同时支持热备机制，我们通过 Nacos 的服务发现机制监测录制调度服务的运行状态，具体关系可以参考下图：

![image](https://user-images.githubusercontent.com/87458342/127258860-22903aed-7aeb-462b-9781-75da7d6ada33.png)

### 3.1 CPU、磁盘

CPU 负载过高和磁盘空间不足是最为常见的两种录制时的异常状况，大致的处理逻辑也是较为相似的。

CPU 过高的处理逻辑，可以参考下图：

![image](https://user-images.githubusercontent.com/87458342/127258876-e145330c-5e81-4a9a-874a-4243c2513c1b.png)

当前机器接收到任务后，进行自检操作，发现 CPU 负载过高会停止当前录制任务的执行，同时上报调度服务，重新分配别的机器执行该录制任务。

当前机器正在执行录制任务，突然发现 CPU 负载超过阈值，会持续观察一段时间，假定观察周期为 10 秒，如果 CPU 负载连续 10 秒钟超高，那么会停止当前录制任务，同时上报调度服务，请求别的机器继续执行该录制任务，最后将两台机器上的录制文件进行逻辑关联保存到数据库中。如果 CPU 负载在 10 秒内恢复到正常值，我们将继续执行当前录制任务。

磁盘空间不足的处理逻辑和 CPU 负载过高有类似的处理逻辑，具体可以参考下图：

![image](https://user-images.githubusercontent.com/87458342/127258907-35b3b4d6-e300-492a-87e0-4fe0bd560b2b.png)

通过流程图，我们也可以知道磁盘空间不足的处理逻辑和 CPU 负载过高时类似，上图已经展示的非常明确了，这里就不过多赘述了。

### 3.2 异常处理

一些其他的异常处理情况，比如崩溃，整体流程可以参考下图：

![image](https://user-images.githubusercontent.com/87458342/127258949-01491b74-1684-403d-ac5b-711c6accb1ea.png)

异常发生时，如果是一般异常，我们只需要将状态通知调度服务即可，调度服务记录相关日志，综合分析整个录制服务的状态。如果 60%的录制机器触发了相同的异常，调度服务就要采取相应的策略。如果是崩溃等重大异常，就需要重启机器或者调度新的机器继续执行录制任务。

### 3.3 录制超时

如果发生了录制超时，比如我们想录制 24 个小时的视频，现在时长已经录够了，接下来应该怎么做呢？一般有两种处理方法，第一种是直接停止当前录制，上报通知调度服务即可，这种处理方式比较简单粗暴，但是在安防和监控领域是不合适的。第二种是执行特定规则的覆盖策略，实现循环覆盖，始终保留最近 24 小时之内的视频画面内容。

![image](https://user-images.githubusercontent.com/87458342/127258966-0fe7e69c-4a29-4baf-b1f5-baa1c19597f0.png)

对比上述两种处理方式，当发生录制超时时，第二种方式是最符合安防和监控领域的通用做法。那么覆盖策略又是怎么实现的呢，这就引出了下面的内容——覆盖策略。

## 4. 覆盖策略

覆盖策略在原理上理解起来很简单，但是具体执行时，就不那么简单了。首先，我们也先通过一个流程图对覆盖策略的处理逻辑有一个整体上的认识。

![image](https://user-images.githubusercontent.com/87458342/127258979-f78e4f55-13dc-4c2c-8426-81c27f8d0780.png)


### 4.1 一级定时器

当录制任务启动时，我们同时启动一个定时器（一级定时器），定时器的时长就是录制任务的目标时长，这个非常好理解。但是，这个定时器只生效一次或者一次都不生效。只有一级定时器生效后，才会启动二级定时器。如果一级定时器没有启动，那么二级定时器也不会启动。

我们可以这样理解，只有一级定时器触发，录制服务才会执行对应的覆盖策略。当覆盖策略启动后，一级定时器销毁，二级定时器生效。

### 4.2 二级定时器

当文件时长达到了预设的最大时长时，我们将启动二级定时器。其实，二级定时器控制的是覆盖策略的删除频率，每次时间到了，就删除早些时候到录制文件分片。

### 4.3 执行覆盖

具体覆盖的执行逻辑是，根据 ts 分片的时长和二级定时器的时间周期，计算需要删除的 ts 分片个数，同时更新 m3u8 中的索引列表，然后循环执行该策略，最终实现动态循环的录制覆盖策略。

![image](https://user-images.githubusercontent.com/87458342/127258989-5ae6ab9b-ab25-4ab6-afd8-c2e915024dd3.png)

覆盖策略的执行过程如上图所示，相信通过上文的解释，大家理解起来还是非常容易的。需要特别说明的是，由于二级定时器执行周期 t 的限制，录制文件的实际时长在最大录制时长 T 和（T+t）之间。

## 结尾

好了，现在关于 RTSP 媒体流的录制方案和覆盖策略就介绍完了，相信大家对云端录制方案也有了一定认识，有自己想法和感兴趣的小伙伴，欢迎评论留言。关注我，分享更多音视频和流媒体服务器内容。

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者：  刘振
