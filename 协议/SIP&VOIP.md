# 参考文献
- [通信系列2: 深入浅出SIP协议](https://blog.csdn.net/c80486/article/details/43391961)
# 1. 什么是VOIP
&emsp;&emsp;VoIP可以简单理解为一种用因特网系统代替传统电话通讯系统进行语音通话的技术。VOIP和传统语音通话技术的最大区别是：
- 传统语音通话采用的是模拟信号技术，模拟信号容易受到干扰，很难避免信号失真，另外，传统模拟信号通话技术的容量受到很多限制，所以信号是经过高失真压缩的，因此效果不会很理想；
- VOIP采用的是数字传输技术，在网络上传输的是包含语音信息的数据包，可以进行低失真压缩，这些数据包只要被对方收到并按约定的规则还原为语音信号，失真度一般都比较小（失真主要产生于录音设备和扬声器上）。VOIP中国进行数据传输的依赖的协议就是SIP

# 2. 什么是SIP

## 2.1 关于SIP通话的一个形象比喻

生活中，我们想要找一个人互相聊天，首先你到找到这个人、你的声音得传递到对方，对方能听到你的声音，同时还得要去理解您的话语（同一个方言、同一个语种），帮你定位到对象的是sip，你们两个协商使用英语沟通、还是汉语，使用电话设备、还是电脑web的是sdp， 最终说话的传递及传输介质是rtc。sip协议做的是，能够让你定位到你想聊天的对象， 帮你检测你聊天对象是否可达， 帮你管理你们通话的会话状态，帮你结束你们的聊天进程等。

从上面的例子可以看出，SIP协议只是负责创建会话，但是会话中的实际业务数据的传输并不由SIP来完成，而是由RTP来完成的。整个会话的实现不仅是需要SIP协议，还需要其他的协议，比如SDP和RTC等。

## 2.2 SIP协议的概念和特点

**(1) SIP协议的概念**
&emsp;&emsp;SIP全称是session initiation Protocol (会话初始协议) ，他是一个基于文本的应用层控制协议，用于创建、修改和释放一个或多个参与者的会话。SIP 是一种源于互联网的IP 语音会话控制协议，具有灵活、易于实现、便于扩展等特点

- **应用层**：在网络通信中最上层，实现业务的具体功能。
- **信令**：信息指令，**表示协议的传输的内容是指令而非业务数据**。
- **控制**：执行什么样的操作。

**(2) SIP协议的特点**

- 不定义要建立的会话类型，只定义如何管理会话。 SIP不是一个垂直集成的通讯系统，可能叫做是一个部件更合适。
- SIP本身并不提供服务。但是，SIP提供了一个基础，可以用来实现不同的服务。

## 2.3 SIP协议的通信流程

> **SIP协议通信过程中并不包含业务数据**

**(1) SIP通信的3个基本组件**

- 用户代理（User Agent Client）,即终端。比如SIP电话。
- 代理服务器（User Agent Server），负责连接用户代理和查询终端定位。
- 注册服务器。是一个保存了用户代理（终端）信息服务器。可以理解为一个通讯录。

应用示例，终端A拨打终端B电话（**前提是A和B都已经注册到可响应的服务器上**）：

- 终端A先联系代理服务器，把B给到proxy，告诉它我是联系B。
- proxy它自己不知道，就去翻通讯录（即注册服务器），找到B对应的IP地址，并拨打B。
- B接听电话后，proxy就把A和B联通。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/33bb0f0ab08e41f5b350f213d2db4715.png)

**(2) SIP在发起会话时信令交互的流程**
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba46c8b5f61c45a5be3cedee3c0acc1d.png)
SIP协议是一个Client/Sever协议，因此SIP消息分两种：**请求消息和响应消息**。请求消息是SIP客户端为了激活特定操作而发给服务器端的消息。常用的SIP请求消息如下：

- INVITE：表示主叫用户发起会话请求，邀请其他用户加入一个会话。也可以用在呼叫建立后用于更新会话（此时该INVITE又称为Re-invite）。
- ACK：客户端向服务器端证实它已经收到了对INVITE请求的最终响应。
- PRACK：表示对1xx响应消息的确认请求消息。
- BYE：表示终止一个已经建立的呼叫。
- CANCEL：表示在收到对请求的最终响应之前取消该请求，对于已完成的请求则无影响。
- REGISTER：表示客户端向SIP服务器端注册列在To字段中的地址信息。
- OPTIONS：表示查询被叫的相关信息和功能。

> 上述的过程中并没有trying信令，其实在实际的抓包过程中可以发现callee收到INVITE信令后会向caller发送一个t、Trying信令，之后再发送Ringing和OK信令

SIP协议中的响应消息用于对请求消息进行响应，指示呼叫的成功或失败状态。

## 2.4 在不同的网络中建立SIP会话

### 2.4.1 在同一域中建立 SIP 会话

下图说明了在预定同一个 ISP， 从而使用同一域的两个用户之间建立 SIP 会话的过程。用户 A 使用 SIP 电话。用户 B 有一台 PC，运行支持语音和视频的软客户程序。加电后，两个用户都在 ISP 网络中的 SIP 代理服务器上注册了他们的空闲情况和 IP 地址。用户 A 发起此呼叫，告诉 SIP 代理服务器要联系用户 B。然后，SIP 代理服务器向 SIP 注册服务器发出请求，要求提供用户 B 的 IP 地址，并收到用户 B 的 IP 地址。**SIP 代理服务器转发用户 A 与用户 B 进行通信的邀请信息（使用 SDP）**，包括用户 A 要使用的媒体。用户 B 通知 SIP 代理服务器可以接受用户 A 的邀请，且已做好接收消息的准备。SIP 代理服务器将此消息传达给用户 A，从而建立 SIP 会话。然后，**用户创建一个点到点 RTP 连接**，实现用户间的交互通信。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3d2301d59ac544c78ad0a804afbd7311.png)

### 2.4.2 在不同的域中建立 SIP 会话

## 2.5 SIP URL账号

&emsp;&emsp;通话双方都要有一个SIP帐号（也称为URI,  是网络上的电话号码），不同于全数字的传统电话号码， SIP帐号采用 URI 表示方法， 例如：sip:peter@company.com:5060
其中：  

- sip: 表示采用sip协议
- peter是用户名, 也称为帐号.  用字母和数字均可。
- company.com 是帐号所属的服务器域名( 也可以用IP地址，例如: sip:peter@192.168.1.100)
- 最后的5060是端口号。 

SIP协议默认端口为5060,  默认采用UDP传输 。5060的意思是，客户端在名为company.com的服务器的5060端口号上等待对方连接如果端口号是 5060，也可以省略不写。则上述SIP帐号写为:   sip:peter@company.com。除了sip:这几个字母， SIP帐号就像一个邮件帐号。 没错，SIP协议设计者的意图就是让SIP帐号与邮件帐号一致，方便与邮箱服务整合。对用户来说方便，你要打电话给我，我的电话号就是邮箱。

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
### 4.3.3 200 OK
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