# 参考资料
- [会话初始协议SIP与SDP简介](https://cloud.tencent.com/developer/news/387488)
- [完整SIP/SDP媒体协商概论-SIP/WebRTC概要](http://www.ctiforum.com/news/guandian/568193.html)

# 1. 什么是SDP
&emsp;**SDP的作用就是在媒体会话中，传递媒体流信息，允许会话描述的接收者去参与会话**。SDP基本上在internet上工作。他定义了会话描述的统一格式,但并不定义多播地址的分配和SDP消息的传输,也不支持媒体编码方案的协商,这些功能均由下层传送协议完成。典型的会话传送协议包括:SAP(Session Announcement Protocol会话公告协议),SIP(Session Initiation Protocol，会话初始协议),RTSP,HTTP,和使用MIME的E-Mail。