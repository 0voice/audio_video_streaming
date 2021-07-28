# WebRTC 传输安全机制第二话：深入显出 SRTP 协议

通过 DTLS 协商后，RTC 通信的双方完成 MasterKey 和 MasterSalt 的协商。接下来，我们继续分析在 WebRTC 中，如何使用交换的密钥，来对 RTP 和 RTCP 进行加密，实现数据的安全传输。同时，本文会对 libsrtp 使用中，遇到的问题的进行解答，例如，什么是 ROC，ROC 为什么是 32-bits？为什么会返回 error_code=9, error_code=10？交换的密钥有生命周期吗，如果有是多长时间呢？阅读本篇之前建议阅读 DTLS 协商篇，两者结合，效果更佳哦！

## 要解决的问题

RTP/RTCP 协议并没有对它的负载数据进行任何保护。因此，如果攻击者通过抓包工具，如 Wireshark，将音视频数据抓取到后，通过该工具就可以直接将音视频流播放出来，这是非常恐怖的事情。

在 WebRTC 中，为了防止这类事情发生，没有直接使用 RTP/RTCP 协议，而是使用了 SRTP/SRTCP 协议 ，即安全的 RTP/RTCP 协议。WebRTC 使用了非常有名的 libsrtp 库将原来的 RTP/RTCP 协议数据转换成 SRTP/SRTCP 协议数据。
SRTP 要解决的问题：
* 对 RTP/RTCP 的负载 (payload) 进行加密，保证数据安全；
* 保证 RTP/RTCP 包的完整性，同时防重放攻击。

## SRTP/SRTCP结构

### SRTP 结构

![image](https://user-images.githubusercontent.com/87458342/127289539-407f7e72-2bbf-4e97-bee9-f98488948b2d.png)

从 SRTP 结构图中可以看到：

1. 加密部分 Encrypted Portion，由 payload, RTP padding和RTP pad count 部分组成。也就是我们通常所说的仅对 RTP 负载数据加密。
2. 需要校验部分 Authenticated Portion，由 RTP Header, RTP Header extension 和 Encrypted Portion 部分组成。

通常情况下只需要对 RTP 负载数据进行加密，如果需要对 RTP header extension 进行加密，RFC6904 给出了详细方案，在 libsrtp 中也完成了实现。

### SRTCP 结构

![image](https://user-images.githubusercontent.com/87458342/127289648-009201ad-47b6-4445-800f-c4d9609654dc.png)

从 SRTCP 结构图中可以看到：

1. 加密部分 Encrypted Portion，为 RTCP Header 之后的部分，对 Compound RTCP 也是同样。
2. E-flag 显式给出了 RTCP 包是否加密。（PS：一个 RTP 包怎么判断是加密的？）
3. SRTCP index 显示给出了 RTCP 包的序列号，用来防重放攻击。（PS：一个 RTP 包的 16-bits 的序列号可以防重放攻击吗？）
4. 待校验部分 Authenticated Portion，由 RTCP Header 和 Encrypted Portion 部分组成。

在初步认识了 SRTP 和 SRTCP 的结构后，接下来介绍 Encrypted Portion 和 Authenticated Portion 如何得到了。

## Key管理

在 SRTP/SRTCP 协议中，使用二元组 <SRTP目的IP地址，SRTP/SRTCP目的端口> 的方式来标识一个通信参与者的 SRTP/SRTCP 会话，称为 SRTP/SRTCP Session。

在 SRTP 协议中使用三元组 <SSRC, RTP/RTCP目的地址，RTP/RTCP目的端口> 来标识一个 stream，一个 SRTP/SRTCP Session 由多个 stream 组成。对每个 stream 的加解密相关参数的描述，称为 Cryptographic Context。

每个 stream 的 Cryptographic Context中 中的包含如下参数：

* SSRC: Stream 使用的 SSRC。
* Cipher Parameter：加解密使用的 key, salt，算法描述 (类型，参数等)。
* Authentication Parameter: 完整性使用的 Key, salt，算法描述 (类型，参数等)。
* Anti-Replay Data: 防止重放攻击缓存的数据信息，例如，ROC，最大序号等。

在 SRTP/SRTCP Session 中，每个 Stream 都会使用到属于自己的，加解密 Key，Authentication Key。这些 Key 都是在同一个 Session 中使用到的，称为 Session Key。这些 Session Key 是通过对 Master Key 使用 KDF(Key Derivation Function) 导出的。

KDF 是用于导出 Session Key 函数，KDF 默认使用是加解密函数。例如，在完成 DTLS 后，协商得到的 SRTP 加密算法的 Profile 为：

>SRTP_AES128_CM_HMAC_SHA1_80
>*         cipher: AES_128_CM
>*         cipher_key_length: 128
>*         cipher_salt_length: 112
>*         maximum_lifetime: 2^31
>*         auth_function: HMAC-SHA1
>*         auth_key_length: 160
>*         auth_tag_length: 80

对应的 KDF 为 AES128_CM。Session Key 的导出流程如下图所示：
![image](https://user-images.githubusercontent.com/87458342/127290023-d4a22d0d-6146-4d5b-8326-94eb4ccd9ae6.png)

Session Key 的导出依赖于如下参数：
* key_label: 根据导出的 Key 的类型不同，key_label 取值如下：
![image](https://user-images.githubusercontent.com/87458342/127290065-3f01fc26-ae55-4398-92e6-0e16f3679301.png)

* master_key: DTLS 完成后，协商得到的 Key。
* master_salt:  DTLS 完成后，协商得到的 Salt。
* packet_index:  RTP/RTCP 的包序号。SRTP 使用 48-bits 的隐式包需要，SRTCP 使用 31-bits 包序号。参考序号管理。
* key_derivation_rate: key 导出速率 , 记为 kdr。默认取值为 0，执行 1 次 Key 导出。取值范围 {{1,2,4,...,2^24}。在 key_derivation_rate>0 的情况下，在加密之前，执行一次 key 导出，后续在 packet_index/key_derivation_rate > 0 时，执行 key 导出。

```C++
r = packet_index / kdr
key_id = label || r
x = key_id XOR master_salt
key = KDF(master_key, x)
```

>'/'：表示整除，B=0时，C = A/B=0。<br/>
>||：表示连接的含义。A,B,C使用网络字节序表示，C = A||B, 则C的高字节为A，低字节位为B。<br/>
>XOR：是异或运算，计算时按照低字节位对齐。<br/>

以下使用 AES128_CM，举例说明 Session Key 的导出过程，假设 DTLS 协商得到：

```C++
master_key:  E1F97A0D3E018BE0D64FA32C06DE4139   // 128-bits
master_salt: 0EC675AD498AFEEBB6960B3AABE6           // 112-bits
```

导出加密 Key(cipher key):
```C++
packet_index/kdr:              000000000000
label:                       00
master_salt:   0EC675AD498AFEEBB6960B3AABE6
-----------------------------------------------
xor:           0EC675AD498AFEEBB6960B3AABE6     (x, KDF input)
x*2^16:        0EC675AD498AFEEBB6960B3AABE60000 (AES-CM input)
cipher key:    C61E7A93744F39EE10734AFE3FF7A087 (AES-CM output)
```

导出 SALT Key(cipher salt):
```C++
packet_index/kdr:              000000000000
label:                       02
master_salt:   0EC675AD498AFEEBB6960B3AABE6
----------------------------------------------
xor:           0EC675AD498AFEE9B6960B3AABE6     (x, KDF input)
x*2^16:        0EC675AD498AFEE9B6960B3AABE60000 (AES-CM input)
               30CBBC08863D8C85D49DB34A9AE17AC6 (AES-CM ouptut)
cipher salt:   30CBBC08863D8C85D49DB34A9AE1
```

导出校验 Key(auth key)，需要 auth key 长度为 94 字节：
```C++
packet_index/kdr:                000000000000
label:                         01
master salt:     0EC675AD498AFEEBB6960B3AABE6
-----------------------------------------------
xor:             0EC675AD498AFEEAB6960B3AABE6     (x, KDF input)
x*2^16:          0EC675AD498AFEEAB6960B3AABE60000 (AES-CM input)

auth key                           AES input blocks
CEBE321F6FF7716B6FD4AB49AF256A15   0EC675AD498AFEEAB6960B3AABE60000
6D38BAA48F0A0ACF3C34E2359E6CDBCE   0EC675AD498AFEEAB6960B3AABE60001
E049646C43D9327AD175578EF7227098   0EC675AD498AFEEAB6960B3AABE60002
6371C10C9A369AC2F94A8C5FBCDDDC25   0EC675AD498AFEEAB6960B3AABE60003
6D6E919A48B610EF17C2041E47403576   0EC675AD498AFEEAB6960B3AABE60004
6B68642C59BBFC2F34DB60DBDFB2       0EC675AD498AFEEAB6960B3AABE60005
```

>AES-CM 的介绍，参考AES-CM。

至此，我们得到了 SRTP/SRTCP 加密和认证需要的 Session Key：cipher key，auth key，salt key。

## 序列号管理

### SRTP 序列号管理

在 RTP 包结构定义中使用 16-bit 来描述序列号。考虑到防重放攻击，消息完整性校验，加密数据，导出 SessionKey 的需要，在 SRTP 协议中，SRTP 包的序列号，使用隐式方式来记录包序列号 packet_index，使用 i 标识 packet_index。

对于发送端来说，i 的计算方式如下：
```C++
i = 2^16 * ROC + SEQ
```

其中，SEQ 是 RTP 包中描述的 16-bit 包序号。ROC(rollover  couter) 是 RTP 包序号 (SEQ) 翻转计数，也就是每当 SEQ/2^16=0, ROC 计数加 1。ROC 初始值为 0。

对于接收端来说，考虑到丢包和乱序因素的影响，除了维护 ROC，还需要维护一个当前收到的最大包序号 s_l，当一个新的包到来时候，接收端需要估计出当前包所对应的实际 SRTP 包的序号。ROC 的初始值为 0，s_l 的初始值为收到第一个 SRTP 包的 SEQ。后续通过如下公式，估计接收到的 SRTP 序号 i：

```C++
i = 2^16 * v + SEQ
```

其中，v 可能的取值 { ROC-1, ROC, ROC+1 }，ROC 是接收端本地维护的 ROC，SEQ 是收到 SRTP 的序号。v 分别取 ROC-1，ROC，ROC+1 计算出 i，与 2^16*ROC + s_l  进行比较，那个更接近，v 就取对应的值。完成 SRTP 解密和完整性校验后，更新 ROC 和 s_l，分如下 3 种情况：

1. v = ROC - 1， ROC 和 s_l 不更新。
2. v = ROC，如果 SEQ > s_1，则更新 s_l = SEQ。
3. v = ROC + 1,  ROC = v = ROC + 1，s_l = S

更直观的代码描述：
```C++
if (s_l < 32768)
    if (SEQ - s_l > 32768)
        set v to (ROC-1) mod 2^32
    else
        set v to ROC
    endif
else
    if (s_l - 32768 > SEQ)
        set v to (ROC+1) mod 2^32
    else
        set v to ROC
    endif
endif
return SEQ + v*65536
```

### SRTCP 序列号管理

RTCP 中没有描述序号的字段，SRTCP 的序号在 SRTCP 包，使用 31-bits 中显示描述，详见SRTCP格式，也就是说在 SRTCP 的最大序列号为 2^31。

### 序列号与通信时长

可以看到 SRTP 的序列号最大值为 2^48, SRTCP 的序列号最大值为 2^16。在大多数应用中（假设每 128000 个 RTP 数据包至少有一个 RTCP 数据包），SRTCP 序号将首先达到上限。以 200 SRTCP 数据包 / 秒的速度， SRTCP 的 2^31 序列号空间足以确保大约 4 个月的通信。

## 防重放攻击

攻击者将截获的 SRTP/SRTCP 包保存下来，然后重新发送到网络中，实现了包的重放。SRTP 接收者通过维护一个重放列表 (ReplayList) 来防止这种攻击。理论上 Replay List 应该保存所有接收到并完成校验的包的序列号 index。在实际情况下 ReplayList 使用滑动窗口（sliding window）来实现防重放攻击。使用 SRTP-WINDOW-SIZE 来描述滑动窗口的大小。

### SRTP 防重放攻击

在序列号管理部分，我们详述了接收者，根据接收到的 SRTP 包的 SEQ，ROC，s_l 估算出 SRTP 包的 packet_index 的方法。同时，将接收者已经接收到 SRTP 包的最大序列号，记为 local_packet_index。计算差值 delta：

```C++
delta =  packet_index - local_packet_index
```

分如下 3 种情况说明：

1. delta > 0：表示收到了新的包。
2. delta < -(SRTP-WINDOW-SIZE - 1) < 0：表示收到的包的序列号，小于重放窗口要求的最小序号。libSRTP 收到这样的包时，会返回 srtp_err_status_replay_old=10, 表示收到旧的重放包。
3. delta < 0,  delta >= -(SRTP-WINDOW-SIZE - 1): 表示收到了重放窗口之内的包。如果在 ReplayList 找到对应的包，则是一个 index 重复的重放包。libSRTP 收到这样的包时，会返回 srtp_err_status_replay_fail=9。否则表示收到一个乱序包。

下图更加直观说明防重放攻击的三个区域：

![image](https://user-images.githubusercontent.com/87458342/127290940-a7e0db02-d93d-480b-90f7-35ada64dc3bd.png)

>SRTP-WINDOW-SIZE 的取值，最小是 64。应用可以根据需要设置成较大的值，libsrtp 会向上取整为 32 的整数倍。例如，在 WebRTC 中 SRTP-WINDOW-SIZE = 1024。使用者可以根据需要进行调整，但要达到防重放攻击的目的。

### SRTCP 防重放攻击
在 SRTCP 中，packet index 显式给出。在 libsrtp 中，SRTCP 的防重放攻击的窗口大小为 128。使用 window_start 记录防重放攻击的起始序列号。SRTCP 防重放攻击的检查步骤如下：
1. index > window_start + 128: 收到新的 SRTCP 包。
2. index < window_start: 收到包的序列号在重放窗口的左侧，可以认为我们收到了比较老的包。libsrtp 收到这样的包之后，会返回到 srtp_err_status_replay_old=10。
3. replay_list_index = index - windwo_start：在 ReplayList 中 replay_list_index 对应的标识位为 1，表示已经收到包，libsrtp 返回 srtp_err_status_replay_fail=9。对应的标识位为 0，表示收到乱序包。

### 加密和校验算法

在 SRTP 中，使用了 CTR（Counter mode）模式的 AES 加密算法，CTR 模式通过递增一个加密计数器以产生连续的密钥流，计数器可以是任意保证长时间不产生重复输出的密钥。根据计数方式的不同，分为以下两种类型：

* AES-ICM:  ICM 模式（Integer Counter Mode，整数计数模式），使用整数计数运算。
* AES-GCM: GCM 模式（Galois Counter Mode，基于伽罗瓦域计数模式），计数运算定义在伽罗瓦域。

在 SRTP 中，使用 AES-ICM 完成加密算法，同时使用 HMAC-SHA1 完成 MAC 计算，对数据进行完整性校验，加密和 MAC 计算需要分两步完成。AES-GCM 基于 AEAD（Authenticated-Encryption with Associated-Data，关联数据的认证加密）的思想，在对数据进行加密的同时计算 MAC 值，实现了一个步骤，完成加密和校验信息的计算。下面分别对这个 AES-ICM 和 AES_GSM 的用法进行介绍。

### AEC—ICM

![image](https://user-images.githubusercontent.com/87458342/127291192-dff2b8e5-cfc2-4150-9327-9361f3de7ba9.png)

上图描述了 AES-ICM 的加密和解密过程，图中的 K 是通过 KDF 导出的 SessionKey。加密和加密都是通过对 Counter 进行加密，与明文 P 异或运算得到加密数据 C，反之，与密文 C 异或运算得到明文数据 P。考虑到安全性，Counter 生成依赖于 Session Salt,  包的索引（packet index）和包的 SSRC。Counter 是 128-bits 的计数，生成方式如下定义：

```C++
one byte
<-->
0  1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
|00|00|00|00|   SSRC    |   packet index  | b_c |---+
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+   |
                                                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+   v
|                  salt (k_s)             |00|00|->(+)
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+   |
                                                    |
                                                    v
                                            +-------------+
                    encryption key (k_e) -> | AES encrypt |
                                            +-------------+
                                                    |
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+   |
|                keystream block                |<--+
+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
```

### HMAC—SHA1

散列消息认证码（Hash-based message authentication code，缩写为 HMAC），是一种通过特别计算方式之后产生的消息认证码（MAC），使用密码散列函数，同时结合一个加密密钥，它可以用来保证数据的完整性，同时可以用来作某个消息的身份验证。HMAC 通过一个标准算法，在计算哈希的过程中，把 key 混入计算过程中。HMAC 的加密实现如下：

```C++
HMAC(K,M) = H ( (K XOR opad ) + H( (K XOR ipad ) + M ) )
```

* H：hash 算法，比如，MD5，SHA-1，SHA-256。
* B：块字节的长度，块是 hash 操作的基本单位。这里 B=64。
* L：hash 算法计算出来的字节长度。(L=16 for MD5, L=20 for SHA-1)。
* K：共享密钥，K 的长度可以是任意的，但是为了安全考虑，还是推荐 K 的长度>B。

当 K 长度大于 B 时候，会先在 K 上面执行 hash 算法，将得到的 L 长度结果作为新的共享密钥。如果 K 的长度 <B, 那么会在 K 后面填充 0x00 一直到等于长度 B。

* M：要认证的内容。
* opad：外部填充常量，是 0x5C 重复 B 次。
* ipad：内部填充常量，是 0x36 重复 B 次。
* XOR：异或运算。
* +：代表 " 连接 " 运算。

计算步骤如下：
1. 将 0x00 填充到 K 的后面，直到其长度等于 B。
2. 将步骤 1 的结果跟 ipad 做异或。
3. 将要加密的信息附在步骤 2 的结果后面。
4. 调用 H 方法。
5. 将步骤 1 的结果跟 opad 做异或。
6. 将步骤 4 的结果附在步骤 5 的结果后面。
7. 调用 H 方法。

SRTP 和 SRTCP 计算 Authentication tag，使用的 K 对应 Key 管理部分描述的 RTP auth key 和 RTCP auth key，使用的 Hash 算法为 SHA-1，Authentication tag 的长度为 80-bits。

在计算 SRTP 的，要认证的内容 M 为：

```C++
M = Authenticated Portion + ROC
```

其中，+ 代表 " 连接 " 运算，Authenticated Portion 在 SRTP 的结构图中给出。

在计算 SRTCP 时，要认证的内容 M 为：

```C++
M=Authenticated Portion
```

其中，Authenticated Portion 在 SRTCP 的结构图中给出。

通过使用 Authenticated Portion 算法，计算得到 SRTP/SRTCP 的 Encrypted Portion Portion 部分。

### AES—GCM

AES-GCM 使用计数器模式来加密数据，该操作可以有效地流水线化，GCM 身份验证使用的操作特别适合于硬件中的有效实现。在 GCM-SPEC 详述了 GCM 的理论知识， Section4.2 Hardware 详述了硬件实现。

AES-GCM 在 SRTP 加密中的应用，在RFC7714 进行了详细描述。Key 管理和序列号管理与本文中描述的相同，需要注意的是：

1. AES-GCM 作为一种 AEAD（Authenticated Encryption with Associated Data）加密算法，输入和输出是什么，对应到 SRTP/SRTCP 的包结构中理解。

2. Counter 的是计算方式和 AES-ICM 中描述的计算方式不同，需要重点关注。

* libsrtp 已经实现了 AES-GCM，有兴趣的同学，可以结合代码进行研读。

## libsrtp的使用

* libsrtp 是被广泛使用的 SRTP/SRTCP 加密的开源项目。经常用到的 api 如下：

1. srtp_init，初始化 srtp 库，初始化内部加密算法，在使用 srtp 前，必须要调用了。
2. srtp_create, 创建 srtp_session，可以结合本文中介绍的 session，session key 等概念一起理解。
3. srtp_unprotect/srtp_protect，RTP 包加解密接口。
4. srtp_protect_rtcp/srtp_unprotect_rtcp，RTCP 包的加解密接口。
5. srtp_set_stream_roc/srtp_get_stream_roc, 设置和获取 stream 的 ROC，这两个接口在最新的 2.3 版本加入。

重要的结构 srtp_policy_t，用来初始化加解密参数，在 srtp_create 中使用这个结构。以下参数需要关注：

1. DTLS 协商后得到的 MasterKey 和 MasterSalt 通过这个结构传递给 libsrtp，用于 session key 的生成。
2. window_size，对应我们之前描述的 srtp 防重放攻击的窗口大小。
3. allow_repeat_tx，是否允许重传相同序号的包。

* SRS 是一个新生代实时通信服务器，对 libsrtp 感兴趣的同学，可以快速在本机搭起调试环境，进行相关测试，更加深入理解相关的算法。

## 总结

本文通过对 SRTP/SRTCP 相关原理的深入详细解读，对 libsrtp 使用遇到的问题进行解答，希望能够给实时音视频通信的相关领域的同学以帮助。

## 参考文献

* RFC3711:  SRTP
* RFC6904: Encrypted SRTP Header Extensions
* Integer Counter Mode
* RFC-6188: The Use of AES-192 and AES-256 in Secure RTP
* RFC7714:  AES-GCM for SRTP
* RFC2104:  HMAC
* RFC2202: Test Cases for HMAC-MD5 and HMAC-SHA-1
* GCM-SPEC:  GCM

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

原文作者： 视频云技术
