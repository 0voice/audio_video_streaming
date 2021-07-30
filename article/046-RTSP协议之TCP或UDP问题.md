# RTSP协议之TCP/UDP问题

## 1. 前言

RTSP（Real-Time StreamingProtocol）实时流式协议在直播、流媒体、视频会议等平台用得很多，它是基于TCP/IP开发的上层协议，所以音视频流数据可以用TCP或者UDP来传输。这篇文章目的主要是讲述这二者的区别，如果想了解更多RTSP相关的知识，可以参阅我之前的博文《RTSP协议实例分析》。  

## 2. RTSP之TCP与UDP方式区别 

TCP与UDP方式的区别在客户端项服务端SETUP请求中的Transport项体现。RTSP客户端会根据自己的环境发出请求，以决定使用TCP还是UDP的方式，在比较完善的RTSP服务中这两种方式都支持，然而在我遇到的产品（某品牌NVR）中只支持TCP方式，在实测过程中，VLC连接时默认使用UDP方式连接时会失败，然后VLC会自动切成TCP的连接方式，而FFPALY则不会自动切换，这里为VLC点个赞。

### 2.1 TCP请求方式

TCP请求方式，此方式比较灵活，它不用另外建立音视频传输的Socket，而直接使用RTSP的Socket，这样做可以节省不少资源开支。由于采用TCP传输，数据的可靠性得到保障。在分析抓包数据可以看出客户端在SETUP请求时的数据交互中的Transport项指定了TCP传输方式RTP/AVP/TCP。（以下抓包数据基于VLC RTSP连接NVR获取的音视频流）

```HTML
OPTIONS rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 2
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
 
RTSP/1.0 200 OK
CSeq: 2
Server: Rtsp Server 
Public: OPTIONS, DESCRIBE, SETUP, PLAY, TEARDOWN, SET_PARAMETER
 
DESCRIBE rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 3
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Accept: application/sdp
 
RTSP/1.0 401 Unauthorized
Cseq: 3 
Server: Rtsp Server 0*0*30*4096
WWW-Authenticate: Digest realm="Surveillance Server", nonce="10839044"
 
DESCRIBE rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 4
Authorization: Digest username="admin", realm="Surveillance Server", nonce="10839044", uri="rtsp://192.168.0.49:554/11", response="f1bf854a901dc8a7379ff277ce1be0e3"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Accept: application/sdp
 
RTSP/1.0 200 OK
Cseq: 4 
Server: Rtsp Server 0*0*30*4096
Content-Type: application/sdp
Content-length: 379
Content-Base: rtsp://192.168.0.49/1
 
v=0
o=StreamingServer 3331435948 1116907222000 IN IP4 192.168.0.49
s=h264.mp4
c=IN IP4 0.0.0.0
t=0 0
a=control:*
m=video 0 RTP/AVP 96
a=control:trackID=0
a=rtpmap:96 H264/90000
a=ptime:40
a=range:npt=0-0
a=fmtp:96 packetization-mode=1; sprop-parameter-sets=(null)
a=videoinfo:0*0*30*4096
m=audio 0 RTP/AVP 0
a=control:trackID=1
a=rtpmap:0 PCMU/8000
a=ptime:20
 
SETUP rtsp://192.168.0.49/1/trackID=0 RTSP/1.0
CSeq: 5
Authorization: Digest username="admin", realm="Surveillance Server", nonce="10839044", uri="rtsp://192.168.0.49/1", response="8e69477a4b8602b118a0850dcf3dee51"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Transport: RTP/AVP/TCP;unicast;interleaved=0-1
 
RTSP/1.0 200 OK
CSeq: 5
Server: Rtsp Server 
Session: 06720925;timeout=120
Transport: RTP/AVP/TCP;unicast;interleaved=0-1
 
SETUP rtsp://192.168.0.49/1/trackID=1 RTSP/1.0
CSeq: 6
Authorization: Digest username="admin", realm="Surveillance Server", nonce="10839044", uri="rtsp://192.168.0.49/1", response="8e69477a4b8602b118a0850dcf3dee51"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Transport: RTP/AVP/TCP;unicast;interleaved=2-3
Session: 06720925
 
RTSP/1.0 200 OK
CSeq: 6
Server: Rtsp Server 
Session: 06720925;timeout=120
Transport: RTP/AVP/TCP;unicast;interleaved=2-3
 
PLAY rtsp://192.168.0.49/1 RTSP/1.0
CSeq: 7
Authorization: Digest username="admin", realm="Surveillance Server", nonce="10839044", uri="rtsp://192.168.0.49/1", response="4990f23c2ddd70c8ee8b1711f0588609"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Session: 06720925
Range: npt=0.000-
 
RTSP/1.0 200 OK
CSeq: 7
Server: Rtsp Server 
Session: 06720925;timeout=120
```

### 2.2 UDP请求方式

UDP请求方式，此方式需要多建立两个Socket，用于RTCP、RTP数据传送。分析抓包数据可以看出客户端在SETUP请求时的数据交互中Transport项指定了client_port=64790-64791，它是用来通知服务端与客户端建立Socket通信的。由此可见UDP方式，音视频数据传输与控制信号传输分开，这样导致系统性能开销增大，设计复杂，在嵌入式系统中比较少用。（以下抓包数据基于VLC RTSP连接NVR获取的音视频流）

```HTML
OPTIONS rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 2
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
 
RTSP/1.0 200 OK
CSeq: 2
Server: Rtsp Server 
Public: OPTIONS, DESCRIBE, SETUP, PLAY, TEARDOWN, SET_PARAMETER
 
DESCRIBE rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 3
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Accept: application/sdp
 
RTSP/1.0 401 Unauthorized
Cseq: 3 
Server: Rtsp Server 0*0*30*4096
WWW-Authenticate: Digest realm="Surveillance Server", nonce="07492185"
 
DESCRIBE rtsp://192.168.0.49:554/11 RTSP/1.0
CSeq: 4
Authorization: Digest username="admin", realm="Surveillance Server", nonce="07492185", uri="rtsp://192.168.0.49:554/11", response="e63e8eb892f773c59edaf53e314000b6"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Accept: application/sdp
 
RTSP/1.0 200 OK
Cseq: 4 
Server: Rtsp Server 0*0*30*4096
Content-Type: application/sdp
Content-length: 379
Content-Base: rtsp://192.168.0.49/1
 
v=0
o=StreamingServer 3331435948 1116907222000 IN IP4 192.168.0.49
s=h264.mp4
c=IN IP4 0.0.0.0
t=0 0
a=control:*
m=video 0 RTP/AVP 96
a=control:trackID=0
a=rtpmap:96 H264/90000
a=ptime:40
a=range:npt=0-0
a=fmtp:96 packetization-mode=1; sprop-parameter-sets=(null)
a=videoinfo:0*0*30*4096
m=audio 0 RTP/AVP 0
a=control:trackID=1
a=rtpmap:0 PCMU/8000
a=ptime:20
 
SETUP rtsp://192.168.0.49/1/trackID=0 RTSP/1.0
CSeq: 5
Authorization: Digest username="admin", realm="Surveillance Server", nonce="07492185", uri="rtsp://192.168.0.49/1", response="d559a804fe440390e40fd14251265ebc"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Transport: RTP/AVP;unicast;client_port=64790-64791
 
RTSP/1.0 200 OK
CSeq: 5
Server: Rtsp Server 
Session: 34202454
Transport: RTP/AVP;unicast;client_port=64790-64791;source=192.168.0.49;server_port=32773-0;ssrc=00004E87
 
SETUP rtsp://192.168.0.49/1/trackID=1 RTSP/1.0
CSeq: 6
Authorization: Digest username="admin", realm="Surveillance Server", nonce="07492185", uri="rtsp://192.168.0.49/1", response="d559a804fe440390e40fd14251265ebc"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Transport: RTP/AVP;unicast;client_port=64792-64793
Session: 34202454
 
RTSP/1.0 200 OK
CSeq: 6
Server: Rtsp Server 
Session: 34202454
Transport: RTP/AVP;unicast;client_port=64792-64793;source=192.168.0.49;server_port=32773-0;ssrc=00004E87
 
PLAY rtsp://192.168.0.49/1 RTSP/1.0
CSeq: 7
Authorization: Digest username="admin", realm="Surveillance Server", nonce="07492185", uri="rtsp://192.168.0.49/1", response="e96c9c574774a24a5c372037ffaaad4e"
User-Agent: LibVLC/2.2.8 (LIVE555 Streaming Media v2016.02.22)
Session: 34202454
Range: npt=0.000-
 
RTSP/1.0 200 OK
CSeq: 7
Server: Rtsp Server 
Session: 34202454;timeout=120
```
## 3. 总结

TCP传输方式使用的是原有的套接字，不用另开套接字，起到节省资源的作用，还能利用TCP的可靠性。另一方面它更具有穿墙的特性，在很多网络的路由中有设置不给于外网访问内网，此时用UDP方式，需要服务端主动连接客户端提供的UDP接口，这请求有可能被防火墙拦截，在抓包分析中表现出来是port unreachable的错误提示，可见这种方式是比较受限制的。综上，在可靠连接和资源方面考虑，在嵌入式安防产品中采用TCP方式较多。


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
<br/>



原文作者： dosthing
