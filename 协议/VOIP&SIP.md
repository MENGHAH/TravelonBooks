# 参考文献
- [通信系列2: 深入浅出SIP协议](https://blog.csdn.net/c80486/article/details/43391961)
# 1. 什么是VOIP
&emsp;&emsp;VoIP的完整名字是Voice over Internet Protocol，可以简单理解为一种用因特网系统代替传统电话通讯系统进行语音通话的技术。VOIP和传统语音通话技术的最大区别是：
- 传统语音通话采用的是模拟信号技术，模拟信号容易受到干扰，很难避免信号失真，另外，传统模拟信号通话技术的容量受到很多限制，所以信号是经过高失真压缩的，因此效果不会很理想；
- VOIP采用的是数字传输技术，在网络上传输的是包含语音信息的数据包，可以进行低失真压缩，这些数据包只要被对方收到并按约定的规则还原为语音信号，失真度一般都比较小（失真主要产生于录音设备和扬声器上）。VOIP中国进行数据传输的依赖的协议就是SIP

# 2. 什么是SIP
&emsp;&emsp;很多VIOP产品都叫做SIP通话机，那么SIP又是什么？SIP是一项类似于HTTP的基于文本的网络传输协议。SIP和HTTP两者之间存在很多相似之处，主流的VOIP通讯产品都采用SIP协议作为传输语音数据包的协议。
&emsp;&emsp;<font color=red>上述的表述存在一定的bug， SIP协议规定了会话的发起过程，但没有规定会话的内容及格式。 会话内容可以是文本、语音、视频等。 因此，SIP协议要结合其它协议，如：用SDP协议描述要传递的内容格式，用RTP，RTSP流媒体协议传输媒体，才能完成整个通信过程。 </font>
&emsp;&emsp;<font color=red>SIP协议本身也可以叠加SSL来保证数据传输过程中的安全性。</font>
&emsp;&emsp;<font color=red>SIP协议规定的是点对点的协议(P2P)。 通话内容的过程可以不需要服务器参与。实际运用中，大多数情况都有一个代理服务器(Proxy)，每个软电话与服务器进行SIP通信即可。这个服务器就是电话交换机，所有的消息和话音都可以由这个服务器进行转发。</font>
![在这里插入图片描述](https://img-blog.csdnimg.cn/c52576aadfe241b497f3b9a91698c7b3.png)
<font color=red>VoIP和SIP的关系类似于网站和HTML之间的关系。网站是可通过Web浏览器访问的页面集合。HTML是一种可用于构建网站的语言。但是，大多数网站也使用其他语言，如Java。 类似地，VoIP指的是通过因特网发送语音呼叫的任何方法。这意味着从SparkleComm到企业电话服务的所有内容都属于同一个保护伞。SIP只是当前使用的众多协议之一。因此，您也可以使用其他协议实现VoIP。  </font>
# 3. 如何搭建SIP VoIP
&emsp;&emsp;目前，用户要获得服务必须具备两个条件：一是有宽带接入，整个电话接续都是通过宽带网络传送；另外一个是要有模拟电话适配器(ATA)，通常由VoIP提供者或者运营商来提供。如果是企业用户，也不用购买充当支持数百个同时呼叫的ATA的关口。购买适配器后，用户进行注册，得到所提供的普通电话号码，也可以根据具体情况申请相关的其他增值服务，系统在数据库内把该用户号码与IP地址绑定。客户端使用支持SIP协议的电话或者具有相关电话功能软件的电脑。当一端开始拨号时，系统将从数据库中获取与这个号码绑定的IP地址，接着双方就可以通过因特网或者PSTN网直接通话。

&emsp;&emsp;另外，用户可以申请中继线路自己为SIP电话的“落地”提供市话网络，因此SIP电话用户拨打的用户可以是采用相同技术的SIP用户、也可以是普通的PSTN用户，SIP电话用户也可以接收来自采用相同技术的SIP用户或者普通的PSTN用户的拨打。
# 4. SIP协议分析及通信流程
## 4.1 SIP协议分析

## 4.2 SIP协议的通信流程
![在这里插入图片描述](https://img-blog.csdnimg.cn/c538a10bbcc142bc9a941356f370abb3.png)
1. 软电话A 向 B 发送一个 SIP消息  INVITE， 邀请B通话
2.  软电话B振铃，向A 回复一个SIP消息 RING,  通知 A 正在振铃中，请A等待
3.  软电话B提机，向A发一个SIP消息 OK,  通知 A 可以通话了
4.  软电话A 向 B 回复一个回应消息 ACK，正式启动通话
5. 接下来，双方通话
6. 软电话B挂机，向 A 发一个SIP消息 BYE,   通知 A 通话结束
7. 软电话A 向 B 回复一个消息 OK, 通话结束


## 4.3 SIP的报文格式
### 4.3.1 IVITE报文
一个完整的INVITE报文如下所示：
```bash
INVITE sip:some@192.168.31.131:50027 SIP/2.0
Via: SIP/2.0/UDP 192.168.31.131:51971;rport;branch=z9hG4bKiYblddPPX
Max-Forwards: 70
To: <sip:some@192.168.31.131:50027>
From: <sip:null@null>;tag=Prf3c3Xc
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 1 INVITE
Content-Length: 215
Content-Type: application/sdp
Contact: <sip:null@192.168.31.131:51971;transport=UDP>

v=0
o=user1 685988692 621323255 IN IP4 192.168.31.131
s=-
c=IN IP4 192.168.31.131
t=0 0
m=audio 49432 RTP/AVP 0 8 101
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:101 telephone-event/8000
a=sendrecv
```

类似于HTTP的，SIP协议同样是基于C/S模型来实现。C端发送请求，S端响应。消息传递都是纯文本的。INVITE 是主叫做为Client, 被叫做为Server, 发起的一个请求。请求消息分为很多部分：
- 起始行
- 消息头 : 内部包含一个和多个字段
- 空行 ： 表示头部的结束
- 消息体

**(1) 起始行**
```bash
 INVITE sip:some@192.168.31.131:50027 SIP/2.0
```

请求消息的起始行包括三个参数，格式为： ` Request start-line = Method URI SIP_VERSION`

- **Method**是请求方法，本例是INVITE,   **SIP协议规定的Method有六种: INVITE, ACK, CANCEL用于创建对话，BYE用于结束对话, REGISTER用于登记,OPTIONS用于查询服务器能力**

- **URI**表示所请求的用户或服务器,  也支持 "tel" URI， 本例是sip:some@192.168.31.131:50027

- **SIP_VERSION**是 SIP版本号，本例是 SIP/2.0

**(2) 头部消息**
头部包含一个或多个头字段(head field), 每个字段一行。
一个字段行 ＝ name : value ;  value;

一个请求消息头部至少要包含六个字段：Via, To, From, CSeq, Caller-ID, Max-Forwards

```bash
Via: SIP/2.0/UDP 192.168.31.131:51971;rport;branch=z9hG4bKiYblddPPX

Max-Forwards: 70
To: <sip:some@192.168.31.131:50027>
From: <sip:null@null>;tag=Prf3c3Xc
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 1 INVITE
Content-Length: 215
Content-Type: application/sdp
Contact: <sip:null@192.168.31.131:51971;transport=UDP>
```
### 4.3.2 Ring消息
Ringing消息是被叫对主叫INVITE请求消息的响应消息, 表示被叫正在振铃。
```bash
SIP/2.0 180 Ringing
From: <sip:null@null>;tag=Prf3c3Xc
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 1 INVITE
Via: SIP/2.0/UDP 192.168.31.131:51971;rport=51971;branch=z9hG4bKiYblddPPX
To: <sip:some@192.168.31.131:50027>;tag=AM1g60xRvq
Contact: <sip:192.168.31.131:50027;transport=UDP>
```
首行格式是： SIP协议号 代码 文本描述
Ring消息的代码是180
消息头部其它字段均复制INVITE消息
### 4.3.3 OK
OK消息是被叫对主叫INVITE请求消息的最终响应消息, 表示被叫已提机接听。

```bash
SIP/2.0 200 OK
From: <sip:null@null>;tag=Prf3c3Xc
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 1 INVITE
Via: SIP/2.0/UDP 192.168.31.131:51971;rport=51971;branch=z9hG4bKiYblddPPX
To: <sip:some@192.168.31.131:50027>;tag=AM1g60xRvq
Content-Length: 214
Content-Type: application/sdp
Contact: <sip:192.168.31.131:50027;transport=UDP>

v=0
o=user1 77115499 915054303 IN IP4 192.168.31.131
s=-
c=IN IP4 192.168.31.131
t=0 0
m=audio 49434 RTP/AVP 0 8 101
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:101 telephone-event/8000
a=sendrecv
```

- 首行(start-line)格式是： SIP协议号 代码 文本描述
- OK消息的代码是200
- 消息头部其它字段均复制INVITE消息
- 消息体是被叫的SDP描述

### 4.3.4 ACK 消息
主叫在接收到OK响应消息后，向被叫发送ACK请求消息的响应消息, 正式启动通话。ACK消息没有消息体

```bash
ACK sip:192.168.31.131:50027;transport=UDP SIP/2.0
Via: SIP/2.0/UDP 192.168.31.131:51971;rport;branch=z9hG4bKEfwYu4LbB
To: <sip:some@192.168.31.131:50027>;tag=AM1g60xRvq
From: <sip:null@null>;tag=Prf3c3Xc
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 3 ACK
Max-Forwards: 70
```


### 4.3.5 BYE 消息
通话过程中，主动挂机的一方向另一方发送BYE请示消息，表示请求结束通话。另一方接到BYE消息后，应回复OK消息。
```bash
BYE sip:null@192.168.31.131:51971;transport=UDP SIP/2.0
Via: SIP/2.0/UDP 192.168.31.131:50027;rport;branch=z9hG4bKvtPAT0lfO
To: <sip:null@null>;tag=Prf3c3Xc
From: <sip:some@192.168.31.131:50027>;tag=AM1g60xRvq
Call-ID: cenXTa4i-1423587756904@appletekiAir
CSeq: 711793880 BYE
Max-Forwards: 70
```

## 4.4 VOIP电话系统
 一个VOIP系统中最少有两个部件：  一个SIP服务器,   多个SIP客户端。SIP服务器就相当于电话的交换机来连接各个客户端。