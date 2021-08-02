# WebRTC入门项目

RTCStartupDemo，致力于提供一套超级简单的信令服务器，以及配套的完全基于 WebRTC 官方 API 的客户端 demo 示例代码（含：Web/Android/iOS/Windows 全平台），目标是让所有有兴趣学习 WebRTC 的同学，都能快速把项目 run 起来，看到通话效果，理解核心 API，快速入门。

## 1. 效果图
![image](https://user-images.githubusercontent.com/87458342/127868691-fb7a4e8f-d79b-4ded-94cd-a1c371067d1a.png)

## 2. 目录说明

### RTCSignalServer：

一个简单的 Go 语言版本的 WebRTC 信令服务器，供 demo 使用
该信令服务器的 API 文档：[这里](https://github.com/Jhuster/RTCStartupDemo/tree/master/RTCSignalServer)

### RTCClientDemo：
* Web
* Android
* iOS（coming soon）
* Windows（coming soon）

## 3. 使用方法和限制条件

所有端的 demo 只支持 2 个人在局域网内通话，不同端之间也可以互相通话，比如：Android & Web 之间。
需要配合一台信令服务器，你可以参考项目文档自己编译和部署（推荐），也可以直接使用我部署好的服务器：

>http://rtc-signal.jhuster.com:8080/socket.io

使用我部署的服务器，需要注意如下事项：

仅限于测试和学习，不保证服务器的可用性和稳定性
填写房间号的时候，注意填写一个复杂一点，因为可能会跟网上其他人冲突

### 4. 项目依赖
* [WebRTC](https://webrtc.org/)
* Socket.IO](https://socket.io/)
