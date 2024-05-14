# 参考资料
- [会话初始协议SIP与SDP简介](https://cloud.tencent.com/developer/news/387488)
- [完整SIP/SDP媒体协商概论-SIP/WebRTC概要](http://www.ctiforum.com/news/guandian/568193.html)

#  2. SDP

## 2.1 SDP 介绍

SDP 完全是一种会话描述格式（对应的RFC2327 , RFC4566)。它不属于传输协议 ，它只使用不同的适当的传输协议，包括SIP/RTSP/MIME等。SDP协议也是基于文本的协议，这样就能保证协议的可扩展性比较强，使其具有广泛的应用范围。SDP 不支持会话内容或媒体编码的协商，所以在流媒体中只用来描述媒体信息，媒体协商这一块要用RTSP来实现。
**SDP描叙符包括：**

- 会话名和目的

- 会话激活的时间区段

- 构成会话的媒体

- 接收这些媒体所需要的信息（地址，端口，格式）

- 会话所用的带宽信息

- 会话负责人的联系信息

**媒体信息包括：**

- 媒体类型（视频，音频等）
- 传送协议（RTP/UDP/IP H.320等）
- 媒体格式（H,264视频，MPEG视频等）
- 媒体地址和端口

**SDP协议格式：**
    SDP描述由许多文本行组成，文本行的格式为：<类型>=<值>，<类型>是一个字母，<值>是结构化的文本串，其格式依<类型>而定。
    ＜type＞ = < value > [CRLF]（CRLF表示换行）

- type: 该字节为单字节（如： v，o， m等）区分大小写，=号两侧不允许有空格。

- value: 为结构化文本串

**SDP会话描述：**

- 协议版： v=0， 给出sdp的版本号，目前为0版本,无子版本号本。
- 会话源 ： o=(用户名) (会话标识) (版本) (网络类型) (地址类型) (地址)
  如果不存在用户登录名，该字段标志位“-”会话标识为一随机数字串，版本为该会话公告的版本 \r\n网络类型为文本串，\n “IN’表示internet地址类型为文本串，目前定义为”IP4”和“IP6”两种地址。
- 会话名： s=(会话名) 每个会话描述必须只有一个会话名。
- 会话信息： i=(会话信息) 此字段并非必须，建议包括进来用于描叙相应会话文字性说明，每个会话描叙最多只能有一个。
- URL：u=(URL) 此字段并非必须提供ur的描叙信息
- 连接数据： c=(网络类型) (地址类型) (连接地址)

**SDP时间描述**

- t = （会话活动时间）
- r = * （0或多次重复次数）

**SDP媒体描述**

- m = （媒体名称和传输地址）
- i = * （媒体标题）
- c = * （连接信息 — 如果包含在会话层则该字段可选）
- b = * （带宽信息）
- k = * （加密密钥）
- a = * （0 个或多个会话属性行）

**m描叙行：**

- 格式： m=(媒体)（端口）（传送层）（格式列表）
  - 媒体类型：音频(audio)，视频(video)，应用，数据和控制
  - 端口：媒体传送层端口
  - 传送层：ip4上大多基于rtp/udp上传送（RTP/AVP）、IETF RTP协议，在udp上传输
  - 格式列表： 对应的音频负载类型（PT）

> m=video 0 RTP/AVP 96

**a描叙行：**

- 格式：a=rtpmap:（净荷类型）（编码名）/（时钟速率）[编码参数]可选参数
- a=control:（音/视频连接信息）
- a=control:rtsp://192.168.1.197/h264stream0/trackID=0
- a=rtpmap:96 H264/90000

## 2.2 常用载荷类型

**(1) 音频**

- **PCMU**: 对应的载荷类型为0，代表G.711 μ-law（又称为G711A）音频编码。

- **PCMA**: 对应的载荷类型为8，代表G.711 A-law（又称为G711U）音频编码。

- **Opus**: 对应的载荷类型可以是111或其他值，例如102，Opus是一种开放源代码的音频编解码格式，广泛用于互联网音频传输，具有较低的延迟和高音质特点。

**(2) 视频**

- **H.264**: 这是目前广泛使用的视频编码标准，也称为AVC（Advanced Video Coding）。在SDP中，H.264视频流通常通过"a=rtpmap"行指定，其中媒体名必须为"video"，并且会有一个对应的载荷类型值，**如96或97等**。
- **H.265**: 也称为HEVC（High Efficiency Video Coding），是H.264的后继者，提供更高的压缩效率。在SDP中，H.265视频流同样通过"a=rtpmap"行指定，并有相应的载荷类型值。

## 2.3 实际SDP举例

例1：

```js
// 会话级别描述
v=0//版本
o=- 2205756022 2205756022 IN IP4 127.0.0.1//不存在用户登录名则用‘-’替代、会话标识、版本、网络类型、地址类型、地址
s=EasyDarwin//会话名
i=EasyDarwin//会话信息
c=IN IP4 127.0.0.1//网络类型、地址类型、连接地址
t=0 0//会话活动时间
a=x-qt-text-nam:EasyDarwin//a表示会话属性
a=x-qt-text-inf:EasyDarwin
a=x-qt-text-cmt:source application::EasyDarwin
a=x-qt-text-aut:
a=x-qt-text-cpy:

// 视频媒体级别描述
m=video 0 RTP/AVP 96//媒体、端口、传送层、格式列表
a=rtpmap:96 H264/90000//净荷类型、编码名/时钟速率/编码参数（编码参数项为可选参数）
a=fmtp:96 packetization-mode=1;sprop-parameter-sets=//视频“a=fmpt”字段参考RFC3984的8.2节
a=control:trackID=1//音/视频连接信息

// 音频媒体级别描述
m=audio 0 RTP/AVP 97//媒体、端口、传送层、格式列表
a=rtpmap:97 MPEG4-GENERIC/44100/2//净荷类型、编码名/时钟速率/编码参数（编码参数项为可选参数）
a=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=1210//音频“config”参数解析参考RFC3016
a=control:trackID=2//音/视频连接信息
```

例2：

```js
// 会话级别的描述
v=0
o=- 1586545639954157 1586545639954157 IN IP4 192.17.1.63
s=Media Presentation
e=NONE
b=AS:5100
t=0 0
a=control:rtsp://192.17.1.63:554/
// 视频媒体级别的描述
m=video 0 RTP/AVP 96
c=IN IP4 0.0.0.0
b=AS:5000
a=recvonly
a=x-dimensions:1920,1080
a=control:rtsp://192.17.1.63:554/trackID=1
a=rtpmap:96 H264/90000
a=fmtp:96 profile-level-id=420029; packetization-mode=1; sprop-parameter-sets=Z01AKI2NQDwBE/LgLcBAQFAAAD6AAAw1DoYACYFAABfXgu8uNDAATAoAAL68F3lwoA==,aO44gA==
// 音频级别的媒体描述
m=audio 0 RTP/AVP 8
c=IN IP4 0.0.0.0
b=AS:50
a=recvonly
a=control:rtsp://192.17.1.63:554/trackID=2
a=rtpmap:8 PCMA/8000
a=Media_header:MEDIAINFO=494D4B48010300000400000111710110401F000000FA000000000000000000000000000000000000;
a=appversion:1.0
```

