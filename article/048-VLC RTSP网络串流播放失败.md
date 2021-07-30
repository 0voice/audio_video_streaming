# VLC RTSP网络串流播放失败

## 问题描述：

VLC播放RTSP网络串流失败，没有音视及图像。用wireshark网络抓包分析，发现网络Socket异常中断，初步分析是RTSP协议TCP/UDP问题。

![image](https://user-images.githubusercontent.com/87458342/127636119-5c5e5799-a696-4df6-93e1-f2356f78073f.png)

## 解决方法：

* 1. 打开VLC工具->偏好设置
* 2. 输入/编解码器->RTP over RTSP(TCP)->保存退出

![image](https://user-images.githubusercontent.com/87458342/127636154-50db5cd7-89d8-4464-9853-c5f6b041778c.png)

![image](https://user-images.githubusercontent.com/87458342/127636176-3b56d314-89e3-4607-8c7a-d9888dc1dc10.png)

## 解决效果：

VLC能正常播放RTSP网络串流，再次wireshark网络抓包效果如图：

![image](https://user-images.githubusercontent.com/87458342/127636218-9a7d4fe1-475e-48a2-b86c-4aece21c4a55.png)
