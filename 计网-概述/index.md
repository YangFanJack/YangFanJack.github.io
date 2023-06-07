# 计网-概述


## 计算机网络在信息时代的作用

连通性，信息共享，软硬件共享

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121151622.png)

## 因特网概述

* 网络 network：结点（计算机，交换机）+链路

* 互联网 internet（数量距离相对网络增大）：网络+路由器

* 因特网 Internet：全球最大的互联网

* 因特网发展三个阶段：
  * ARPANET向互联网发展
  * 三级结构的因特网（校园网，地区网，主干网）
  * 多层次的ISP结构的因特网
* 因特网的**标准化**工作由**因特网协会**定义：因特网研究部，因特网工程部

## 因特网组成

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121153542.png" style="zoom:67%;" />

C/S：客户机/服务器下载东西，客户机越多下载越慢

P2P：结点越多下载越快

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121153601.png" style="zoom:67%;" />

电路交换：需要建立连接搭建专线（建立连接-》通话-》释放资源），适用于实时通讯（打电话）

分组交换：不需要建立连接，没有专线。将一个报文拆分为多包，每一个包不一定走同一条路。路由器由存储转发的功能。但是比电路交换延迟更大。

报文交换：和分许交换类似，但是不用拆分。

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121155600.png" style="zoom:67%;" />



## 计算机网络在我国发展

1994年4月20日PRC接入互联网

## 计算机网络的类别

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121160433.png" style="zoom: 67%;" />

局域网：自己购买设备，自己维护，带宽固定。

广域网：花钱买服务，花钱买带宽。

## 计算机网络的性能

* 速率：连接在计算机网络上的主机在数字信道（一个接收端到一个发送端）上的传送位数的速率。b/s,kb/s,Mb/s,Gb/s
* 带宽：数字信道所能传送的**最高**数据率。b/s,kb/s,Mb/s,Gb/s
* 吞吐率：单位时间内通过某个网络的数据量（总的）b/s,Mb/s
* 时延：发送时延，传播时延，处理时延，排队时延
* 时延带宽积：传播时延✖带宽（一个信道承载数据能力）
* 往返时间：从发送方发送数据开始，到发送方收到接收方确认
* 利用率：有数据通过时间/(有无)数据通过时间
* 非性能指标：费用，质量，标准化，可靠性，可扩展性，可升级性，管理与维护

## 计算机网络的体系结构

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121162844.png" style="zoom:67%;" />

**分层的原因：标准化并降低每一层的相互关联**

### OSI7层

* 应用层：能够产生网络流量，能和用户交互的应用程序
* 表示层：加密，压缩，编解码（开发人员需要考虑的问题）
* 会话层：服务器和客户端建立的会话（可以用来查木马：netstat -nb）木马一般时隐藏盗窃信息
* 传输层：可靠传输(建立会话)，不可靠传输(QQ发消息，广播发送)，流量控制
* 网络层：IP地址编址，选择最佳路径
* 数据链路层：数据如何封装，添加物理层mac地址
* 物理层：电压，接口标准

**网络排错：从底层往高层排错**

**网络安全和osi参考模型**

* 物理层安全(连线安全)
* 数据链路层安全(ADSL和AP密码)
* 网络层安全（网关对某些网段的ip限制访问外网）
* 应用层安全（SQL注入漏洞，上传漏洞）

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220522094803.png)

|           应用层            |   传输层   |    网络层    | 数据链路层 | 物理层 |
| :-------------------------: | :--------: | :----------: | :--------: | :----: |
| 应用程序（传输数据单元PDU） | 段（报文） | 包（数据报） |     帧     | 比特流 |

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121181555.png" style="zoom:50%;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121181556.png" style="zoom: 50%;" />


