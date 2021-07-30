# RTSP协议实例分析

## 1. 前言

互联网上关于RTSP的文章很多，但是大多数都是抽象的理论介绍，本文将从实际例子解说RTSP协议，不求面面俱到，但求简单易懂。RTSP（Real-Time Streaming Protocol）实时流式协议是IETF的MMUSIC工作组开发的协议，现在已成为因特网建议标准[RFC 2326]。RTSP是为了给流式过程增加更多的功能（暂停、继续、播放、快进、快退）而设计的协议。需要注意的是，RTSP本身不传输数据，音视频流数据是通过RTP传输的。

## 2. RTSP的请求方法

在开始实例分析前先介绍RTSP很重的概念，RTSP请求方法，顾名思义，就是定义一系列方法来进行客户端与服务端通信。下面枚举是有关于RTSP的请求方法集合：

```C++
typedef enum RtspReqMethod
{
	RTSP_REQ_METHOD_SETUP = 0,
	RTSP_REQ_METHOD_DESCRIBE,
	RTSP_REQ_METHOD_REDIRECT,
	RTSP_REQ_METHOD_PLAY,
	RTSP_REQ_METHOD_PAUSE,
	RTSP_REQ_METHOD_SESSION,
	RTSP_REQ_METHOD_OPTIONS,
	RTSP_REQ_METHOD_RECORD,
	RTSP_REQ_METHOD_TEARDOWN,
	RTSP_REQ_METHOD_GET_PARAM,
	RTSP_REQ_METHOD_SET_PARAM,
	RTSP_REQ_METHOD_EXTENSION,
	RTSP_REQ_METHOD_MAX,
}RtspReqMethod_e;
```

只要了解常用几个就好，其它是为了让协议具有兼容性而拓展的，在实际应用中遇到较少

>* OPTIONS  请求用于返回服务端支持的 RTSP方法列表 。也可以定时发送这个请求来保活相关的 RTSP 会话。
>* DESCRIBE 命令用于请求指定的媒体流的 SDP 描述信息（详细包括音视频流的帧率、编码类型等等媒体信息）
>* SETUP  命令用于配置数据交互的方法。（比如制定音视频的传输方式TCP UDP）
>* PLAY  用于启动 (当暂停时重启) 交付数据给客户端. PLAY 命令的应答消息包含如下附加的头字段:
>* PAUSE  请求用于临时停止服务端的数据的交互。使用 PLAY 来重新启动数据交互。
>* TEARDOWN  请求用于终止来自服务端的数据的传输。

## 3. RTSP的实例抓包分析

好了，有了以上这些知识，可以直接实例分析了，本抓包数据是用wireshark抓取NVR或者IPC RTSP服务端推送过来的流数据，如果没有NVR或者IPC可以用VLC作为RTSP服务器推流进行抓包分析。我们打开wireshark并输入相应的过滤规则（ip.addr==192.168.1.1 && rtsp）开始抓包。然后在VLC输入如rtsp://admin:12345@192.168.1.1:554/10来向服务器请求流。

![image](https://user-images.githubusercontent.com/87458342/127633651-cad4cd3e-0eaf-44d5-8e7e-f1864495f9e0.png)
![image](https://user-images.githubusercontent.com/87458342/127633665-9038a5e9-57ae-4d72-bbdd-25b162ae5bd1.png)
![image](https://user-images.githubusercontent.com/87458342/127633689-04f2d1f8-39ff-41b9-a7d5-2c1e3fb8239a.png)

为了更容易理解，这里再唠叨一下，上面的会话格式遵循RTSP语法：

RTSP 的语法和 HTTP 的语法基本相同, 具体如下：

```HTML
COMMAND rtsp_URL RTSP/1.0<CRLF>
Headerfield1: val1<CRLF>
Headerfield2: val2<CRLF>
...
<CRLF>
[Body]
```

客户端经过TCP三次握手后，客户端发送 OPTIONP的方法询问服务器等提供的服务，此时Cseq为2，它只是记录回话的次数序号而已，可以看到RTSP服务器支持OPTIONS, DESCRIBE, SETUP, PLAY, TEARDOWN, SET_PARAMETER这几种方法。

>* Cseq为3时，则是客户端用DESCRIB方法主动告诉服务器自己的信息，服务器回的是未认证Unauthorized，即未登录。
>* Cseq为4时，客户端用DESCRIB方法主动发送用户名及密码给服务端，用户名为字段username，密码则由nonce和 response加密组成，服务器成功认证的话会发送服务器的媒体信息。
>* Cseq为5时，客户端用SETUP方法主动向服务端请求视频流（trackID=0）。
>* Cseq为6时，客户端用SETUP方法主动向服务端请求音频流（trackID=1）。
>* Cseq为7时，客户端用PLAY方法主动向服务端请求播放，服务端回应200 OK等信息后，开始向客户端推送RTP流。

下面是服务端回应的状态码结构体，跟http请求返回值类型码很类似，有兴趣可以了解一下。

```HTML
RtspMethod_t gRtspStatu[] = {
	{"Continue", 100},
	{"OK", 200},
	{"Created", 201},
	{"Accepted", 202},
	{"Non-Authoritative Information", 203},
	{"No Content", 204},
	{"Reset Content", 205},
	{"Partial Content", 206},
	{"Multiple Choices", 300},
	{"Moved Permanently", 301},
	{"Moved Temporarily", 302},
	{"Bad Request", 400},
	{"Unauthorized", 401},
	{"Payment Required", 402},
	{"Forbidden", 403},
	{"Not Found", 404},
	{"Method Not Allowed", 405},
	{"Not Acceptable", 406},
	{"Proxy Authentication Required", 407},
	{"Request Time-out", 408},
	{"Conflict", 409},
	{"Gone", 410},
	{"Length Required", 411},
	{"Precondition Failed", 412},
	{"Request Entity Too Large", 413},
	{"Request-URI Too Large", 414},
	{"Unsupported Media Type", 415},
	{"Bad Extension", 420},
	{"Invalid Parameter", 450},
	{"Parameter Not Understood", 451},
	{"Conference Not Found", 452},
	{"Not Enough Bandwidth", 453},
	{"Session Not Found", 454},
	{"Method Not Valid In This State", 455},
	{"Header Field Not Valid for Resource", 456},
	{"Invalid Range", 457},
	{"Parameter Is Read-Only", 458},
	{"Internal Server Error", 500},
	{"Not Implemented", 501},
	{"Bad Gateway", 502},
	{"Service Unavailable", 503},
	{"Gateway Time-out", 504},
	{"RTSP Version Not Supported", 505},
	{"Extended Error:", 911},
	{0, RTSP_PARSE_INVALID_OPCODE}
};
```

![image](https://user-images.githubusercontent.com/87458342/127633907-0be292e5-ce8b-492a-b57b-c4dce20700f8.png)

可以看到抓包序列从407到419为客户端与服务端信息交互的过程，从420开始则是服务端用RTP发送过来的音视频流数据。

## 4. RTP音视频数据的载体

RTP（Real-Time Transport Protocol）实时运输协议是IEFT的AVT工作组开发的协议，为实时应用提供端到端的运输服务，但不提供任何服务质量的保证，它有两种工作模式，两者的区别归纳如下：

1. 使用udp传输需要为每一个连接设定本机的rtp和rtcp对应的两个端口用于rtp和rtcp的通讯，而tcp方式不需要。
2. 在收包的过程中，TCP流式和UDP包式的不同。

讲到协议可能会有点蒙，其实RTP协议构造很简单，它就是在音视频数据的头部加上RTP的数据头来区分识别音视频流数据，以确保客户端能正确解析数据而已。RTP协议头数据犹如结构体：

```C++
typedef struct RtpHdr_s
{
 
#if (BYTE_ORDER == LITTLE_ENDIAN)
    /* byte 0 */
    u16 cc      :4;   /* CSRC count */
    u16 x       :1;   /* header extension flag */
    u16 p       :1;   /* padding flag */
    u16 version :2;   /* protocol version */
    /* byte 1 */
    u16 pt      :7;   /* payload type */
    u16 marker  :1;   /* marker bit */
#elif (BYTE_ORDER == BIG_ENDIAN)
    /* byte 0 */
    u16 version :2;   /* protocol version */
    u16 p       :1;   /* padding flag */
    u16 x       :1;   /* header extension flag */
    u16 cc      :4;   /* CSRC count */
    /*byte 1*/
    u16 marker  :1;   /* marker bit */
    u16 pt      :7;   /* payload type */
#else
    #error YOU MUST DEFINE BYTE_ORDER == LITTLE_ENDIAN OR BIG_ENDIAN !
#endif
    /* bytes 2, 3 */
    u16 seqno  :16;   /* sequence number */
    /* bytes 4-7 */
    int ts;            /* timestamp in ms */
    /* bytes 8-11 */
    int ssrc;          /* synchronization source */
}RtpHdr_t; 
```

由英文注释，可以大概了解其意思，我比较关注的是payload type 和marker bit ，payload type定义了RTP帧是视频还是音频，marker bit定义了RTP帧是否结束（RTP报文段必须小于MTU，所以一般的视频都有好几个报文段组成）。

![image](https://user-images.githubusercontent.com/87458342/127634041-6b854769-0a43-4ec8-b932-cdd4b291befe.png)

上图抓取了其中一个报文段来分析RTP协议数据，可以看出这是一帧视频流，而且尚未结束还有其他报文（marker bit为false）。下面再来看一个抓包截图：

![image](https://user-images.githubusercontent.com/87458342/127634071-af3eef33-2680-4176-8ed8-5d44232b0206.png)


前面提及，服务端从420就已经用RTP推送音视频流数据，直到484才收到第一帧视频，其中相隔64个报文段，而接来的视频一般只用5到6个报文段就能传输完成。其实这里涉及一点关于视频编码相关知识，I帧、P帧、B帧等。I帧能完全还原一幅图像，P帧、B帧则是参考其他帧来完成显示，其大小比I帧小很多。这就可以解释上面为什么第一帧视频这么大，而后面几帧就很小的缘故了。视频编码的知识在后续博文中将详细解析，敬请关注我的博客更新。


### 5. 总结

rtsp协议在音视频流传输上具有很高的地位，在直播平台、流媒体平台、安防监控中使用较多，学会抓包分析rtsp连接问题，能事半功倍解决问题。原创不易，请点赞，转载说明出处。

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


原文作者： dosthing

