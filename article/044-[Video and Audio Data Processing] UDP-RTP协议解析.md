# [Video and Audio Data Processing] UDP-RTP协议解析

## 概念

网络协议数据在视频播放器中的位置如下所示。

![image](https://user-images.githubusercontent.com/87458342/127628754-7d320f29-17d3-4529-b72c-22151a8a8e82.png)

MPEG-TS封装格式数据打包为RTP/UDP协议然后发送出去的流程如下图所示。图中首先每7个MPEG-TS Packet打包为一个RTP，然后每个RTP再打包为一个UDP。其中打包RTP的方法就是在MPEG-TS数据前面加上RTP Header，而打包RTP的方法就是在RTP数据前面加上UDP Header。

![image](https://user-images.githubusercontent.com/87458342/127628789-bf621ed4-9515-4ae2-91d1-152362aacc86.png)

基本概念就差不多这些，更多的请看我写的代码注释，以及本文参考链接。

## 代码

```C++
extern "C"
{
#ifdef __cplusplus
#define __STDC_CONSTANT_MACROS

#endif

}
extern "C" {


#include <stdio.h>
#include <winsock2.h>
}



// 摘自<winsock.h>
// typedef struct WSAData {
//         WORD                    wVersion;
//         WORD                    wHighVersion;
// #ifdef _WIN64
//         unsigned short          iMaxSockets;
//         unsigned short          iMaxUdpDg;
//         char FAR *              lpVendorInfo;
//         char                    szDescription[WSADESCRIPTION_LEN+1];
//         char                    szSystemStatus[WSASYS_STATUS_LEN+1];
// #else
//         char                    szDescription[WSADESCRIPTION_LEN+1];
//         char                    szSystemStatus[WSASYS_STATUS_LEN+1];
//         unsigned short          iMaxSockets;
//         unsigned short          iMaxUdpDg;
//         char FAR *              lpVendorInfo;
// #endif
// } WSADATA;



#pragma comment(lib, "ws2_32.lib") 

#pragma pack(1)

/*
 * [memo] FFmpeg stream Command:
 * ffmpeg -re -i sintel.ts -f mpegts udp://127.0.0.1:8880
 * ffmpeg -re -i sintel.ts -f rtp_mpegts udp://127.0.0.1:8880
 */

typedef struct RTP_FIXED_HEADER {
	/* byte 0 */
	unsigned char csrc_len : 4;       /* expect 0 */
//CSRC记数（CC）    表示CSRC标识的数目
	unsigned char extension : 1;      /* expect 1 */
//extension：扩展位
	unsigned char padding : 1;        /* expect 0 */
//padding: 填充位， 一些具有固定块大小的加密算法
//或在较低层协议数据单元中携带几个RTP数据包可能需要填充。
	unsigned char version : 2;        /* expect 2 */
//version字段标识RTP的版本
	/* byte 1 */
	unsigned char payload : 7;
//payload：负载类型,标明RTP负载的格式，
//包括所采用的编码算法、采样频率、承载通道等
	unsigned char marker : 1;        /* expect 1 */
//标记位，处理帧边界之类的东西
	/* bytes 2, 3 */
	unsigned short seq_no;
// seq_no: 序列号用来为接收方提供探测数据丢失的方法，
// 但如何处理丢失的数据则是应用程序自己的事情，RTP协议本身并不负责数据的重传。
	/* bytes 4-7 */
	unsigned  long timestamp;
// timestamp: 时间戳，记录了负载中第一个字节的采样时间，
//接收方能够根据，时间戳确定数据的到达是否受到了延迟抖动的影响，
// 但具体如何来补偿延迟抖动则是应用程序自己的事情。
	/* bytes 8-11 */
	unsigned long ssrc;            /* stream number is used here. */
//  If a participant generates multiple streams in one RTP session, 
//  for example from separate video cameras, each MUST be identified 
//  as a different SSRC.
} RTP_FIXED_HEADER;


//这里使用了位域的结构
//这个MPEGTS_FIXED_HEADER结构的长度是4个字节
typedef struct MPEGTS_FIXED_HEADER {
	unsigned sync_byte : 8; 
//Sync byte:同步字节，值为0x47；
	unsigned transport_error_indicator : 1;
//Transport error indicator:传输错误指示位，
// 置1时，表示传送包中至少有一个不可纠正的错误位。
	unsigned payload_unit_start_indicator : 1;
//Payload_unit start indicator:负载单元起始指标位，
//表示TS包的有效净荷以PES/PSI包的第一个字节开始，
//举个例子，一个PES包可能由多个TS包构成，
//第一个TS包的负载单元起始指标位才会被置位。
	unsigned transport_priority : 1;
//Transport priority:传输优先级，\
//表明该包比同个PID的但未置位的TS包有更高的优先级。
	unsigned PID : 13;
//PID:该TS包的ID号，如果净荷是PAT包，则PID固定为0x00。
	unsigned scrambling_control : 2;
//Transport scrambling control:传输加扰控制位
	unsigned adaptation_field_exist : 2;
//Adaption field control:自适应调整域控制位，
//置位则表明该TS包存在自适应调整字段。
	unsigned continuity_counter : 4;
// Continuity counter:连续计数器，随着具有相同PID的TS包的增加而增加，
// 达到最大时恢复为0，如果两个连续相同PID的TS包具有相同的计数，
// 则表明这两个包是一样的，只取一个解析即可。
} MPEGTS_FIXED_HEADER;



int simplest_udp_parser(int port)
{
	WSADATA wsaData;
//WSADATA结构被用来保存AfxSocketInit函数返回的WindowsSockets初始化信息。
	WORD sockVersion = MAKEWORD(2, 2);
//这个宏呢就是返回一个word大小的内存 高位是2 低位也是2 所以版本是2.2
//typedef  unsigned short     word;   /* Unsinged 16 bit value type. */
	int cnt = 0;

	//FILE *myout=fopen("output_log.txt","wb+");
	FILE* myout = stdout;

	FILE* fp1 = fopen("output_dump.ts", "wb+");

	if (WSAStartup(sockVersion, &wsaData) != 0) {
// 对2.2版本Windows Sockets 库进行初始化
// 如果出错zhi,则返回0
		return 0;
	}

//Windows下初始化Winsock的模板写法
	SOCKET serSocket = socket(AF_INET, SOCK_DGRAM, IPPROTO_UDP);
	if (serSocket == INVALID_SOCKET) {
		printf("socket error !");
		return 0;
	}

// typedef struct sockaddr_in {
// 
// #if(_WIN32_WINNT < 0x0600)
//     short   sin_family;
// #else //(_WIN32_WINNT < 0x0600)
//     ADDRESS_FAMILY sin_family;
// #endif //(_WIN32_WINNT < 0x0600)
// 
//     USHORT sin_port;
//     IN_ADDR sin_addr;
//     CHAR sin_zero[8];
// } SOCKADDR_IN, *PSOCKADDR_IN;

//#define INADDR_ANY    (ULONG)0x00000000


//这块是socket网络编程的东西，大概就是要绑定本地端口，然后接收数据
	sockaddr_in serAddr;
	serAddr.sin_family = AF_INET;
	serAddr.sin_port = htons(port);
	serAddr.sin_addr.S_un.S_addr = INADDR_ANY;
	if (bind(serSocket, (sockaddr*)&serAddr, sizeof(serAddr)) == SOCKET_ERROR) {
		printf("bind error !");
		closesocket(serSocket);
		return 0;
	}

	sockaddr_in remoteAddr;
	int nAddrLen = sizeof(remoteAddr);

	//How to parse?
	int parse_rtp = 1;
	int parse_mpegts = 1;

	printf("Listening on port %d\n", port);

	char recvData[10000];
	while (1) {

//recvfrom函数用于从（已连接）套接口上接收数据，并捕获数据发送源的地址。
//recvData接收数据缓冲区
//返回值:成功则返回接收到的字符数,失败返回-1.
//使用 recvfrom 函数接收到的数据已经是UDP的负载了，没有头部信息了。
//所以不要困惑为啥没解析 UDP文件头
		int pktsize = recvfrom(serSocket, recvData, 10000, 0, (sockaddr*)&remoteAddr, 
		&nAddrLen);
		if (pktsize > 0) {
			//printf("Addr:%s\r\n",inet_ntoa(remoteAddr.sin_addr));
			//printf("packet size:%d\r\n",pktsize);
			//Parse RTP
			//
			if (parse_rtp != 0) {
				char payload_str[10] = { 0 };
				RTP_FIXED_HEADER rtp_header;
				int rtp_header_size = sizeof(RTP_FIXED_HEADER);
				//RTP Header
				memcpy((void*)&rtp_header, recvData, rtp_header_size);
			//rtp_header从接收到的recvData数据里面，获取RTP头信息
				

				//RFC3551
				char payload = rtp_header.payload;
				switch (payload) {
				case 0:
				case 1:
				case 2:
				case 3:
				case 4:
				case 5:
				case 6:
				case 7:
				case 8:
				case 9:
				case 10:
				case 11:
				case 12:
				case 13:
				case 14:
				case 15:
				case 16:
				case 17:
				case 18: sprintf(payload_str, "Audio"); break;
				case 31: sprintf(payload_str, "H.261"); break;
				case 32: sprintf(payload_str, "MPV"); break;
				case 33: sprintf(payload_str, "MP2T"); break;
				case 34: sprintf(payload_str, "H.263"); break;
				case 96: sprintf(payload_str, "H.264"); break;
				default:sprintf(payload_str, "other"); break;
				}

				unsigned int timestamp = ntohl(rtp_header.timestamp);
			//timestamp:时间戳
				unsigned int seq_no = ntohs(rtp_header.seq_no);
			//seq_no序列号

				fprintf(myout, "[RTP Pkt] %5d| %5s| %10u| %5d| %5d|\n", cnt, 
				payload_str, timestamp, seq_no, pktsize);

				//RTP Data
				char* rtp_data = recvData + rtp_header_size;
				//RTP Data数据从recvData+ rtp_header_size的地址开始获取，没毛病
			
				int rtp_data_size = pktsize - rtp_header_size;
				fwrite(rtp_data, rtp_data_size, 1, fp1);
			//写入到output_dump.ts里面

				//Parse MPEGTS
				if (parse_mpegts != 0 && payload == 33) {
				//33指的是负载类型MP2T
					MPEGTS_FIXED_HEADER mpegts_header;
					for (int i = 0; i < rtp_data_size; i = i + 188) {
						//TS packet一般是188个字节长度，这是此处取188的原因
						if (rtp_data[i] != 0x47)
						//0x47为MPEGTS_FIXED_HEADER头的同步字
							break;
						//MPEGTS Header
						//memcpy((void *)&mpegts_header,
						//rtp_data+i,sizeof(MPEGTS_FIXED_HEADER));
						fprintf(myout, "   [MPEGTS Pkt]\n");
					}
				}

			}
			else {
				fprintf(myout, "[UDP Pkt] %5d| %5d|\n", cnt, pktsize);
				fwrite(recvData, pktsize, 1, fp1);
			}

			cnt++;
		}
	}
	closesocket(serSocket);
	WSACleanup();
	fclose(fp1);

	return 0;
}


int main()
{
	simplest_udp_parser(8880);
	return 0;
}

```

先在visual studio编译运行，

![image](https://user-images.githubusercontent.com/87458342/127628880-4d2f2e81-4fc8-495c-93b9-71c4a87c3b51.png)

然后把sintel.ts放在ffmpeg.exe同级目录，使用ffmpeg推流。

```C++
ffmpeg -re -i sintel.ts -f rtp_mpegts udp://127.0.0.1:8880
```

如下会一边推流一边解析文件。

![image](https://user-images.githubusercontent.com/87458342/127628989-9e2679da-a291-45a0-845c-c5810b822aba.png)


<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者： 1byte ≠ 8bit
