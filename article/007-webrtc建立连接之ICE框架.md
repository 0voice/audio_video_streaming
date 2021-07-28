# webrtc建立连接之ICE框架

## ICE介绍

ICE全称Interactive Connectivity Establishment，即交互式连通建立方式，它是根据RFC5245实现，是一组基于Offer/Answer模式解决NAT穿越打洞的协议集合

## ICE架构

![image](https://user-images.githubusercontent.com/87458342/127259178-1d26f3a2-af6c-4065-91bb-b14df0b9695d.png)

ICE框架如上图所示，Peer A和Peer B通过STUN/TURN，建立连接的过程，它是后续P2P媒体流通讯的基础。

## ICE基本功能

在了解ICE基本功能前，先了解ICE一些基本概念，

* Candidate：媒体传播的候选地址，组成pair做连通性检查，确定传输路径
* Candidate pair ：由本地和远端candidate组成的pair
* Checklist：由candidate pair生成的按优先级排序的链表，用于连通性检查
* Validlist：连通性间检查成功的condidate pair按优先级排序生成链表，用于ICE提名和选择最佳路径

### ICE基本功能：

第一、收集所有的通路
第二、对通路进行连通性检查（先对pair进行排序，然后提名并选择最佳路径）

### 收集所有通路

![image](https://user-images.githubusercontent.com/87458342/127259269-d3414577-90da-4593-bf41-9f5f80cdbd84.png)

如上图所示，candidate类型有三种

* 主机候选者（Local Address）
* 反射候选者（Reflexive Address）
* 中继候选者（Relayed Address）

拿到上面的三类候选者之后，要通过SDP交换数据。
一方收集到上面的所有三类候选者后，通过SDP信令传给对方。
同样另一方收集到候选者后，也做收集工作。
当双方拿到全部列表后，将候选者形成candidate pair

### 连通性检查

1. 对候选对进行优先级排序
2. 对每个候选对进行发送检查
3. 对每个候选对进行接收检查

第一个要进行排序，要把哪一些优先级高的先排队最先进行检测，这样可以节省时间。那在检测的时候呢，首先是要进行发送检测，那发送是OK的时候，然后再测试接收，其实在实际过程中为了这个节省时间是发送跟接收是同时进行的，所以如果我发送出去之后，然后再能收回我自己发送的信息，那么说明整个通路就是通过了，这其实在实现时还是非常简单的，这么说起来呢，就是要分为发送检测和接收检测。

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>


原文作者: webrtc菜鸟笔记
