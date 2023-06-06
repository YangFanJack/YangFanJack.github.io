
**传输层两个协议**的应用场景：

* **TCP**：基于可靠传输的，丢包重传，分段编号，流量控制，建立会话(三次握手)

  * QQ传文件，访问网站，下载文件

* **UDP**：基于不可靠传输，一个数据包就能完成数据通信，不建立会话，支持多播

  * QQ聊天，

* **UDP中IP分片是在IP层完成的， 而TCP分段是在TCP层完成的，TCP在ip层会尽量避免分片**

  **MTU = IP头+【TCP头+(MSS)】**

  TCP层提前完成了分段， 而且， 两个TCP包中都有TCP头， 这是必然的。 所以到了IP层后， 都保留了TCP头， 当然就有了端口信息啊。 这一点与IP层次的分片是不同的。

  ​    而且我们应该看到， 因为TCP包在TCP层已经做了限制， 这就决定了， 等TCP包达到IP层的时候， IP层的数据绝对不会超过MTU,  因此， 对于TCP传输来说， IP层是没有必要分片的， 因为TCP层分段的时候， 已经考虑到了这个限制。

  ​    所以， 我们经常会说， **UDP传输中， IP分片， 是受到了MTU的限制**， **TCP传输中， TCP分段， 是受到了MSS的限制**（实际上最终还是受到了MTU的限制）

* http=TCP+80

  https=TCP+443

  ftp=TCP+25

  SMTP=TCP+25

  POP3=TCP+110

  RDP=TCP+3389

  smb=TCP+445

  SQL Server=TCP+1433

  MySQL=TCP+3306

  DNS=UDP+53 or TCP+53

  Telnet=TCP+23

  SSH=TCP+22

* 应用层协议和服务之间的关系：

  用ip地址定位计算机，用端口来定位服务，服务本质上是运行在TCP或UDP的某个端口侦听客户端请求的软件

  查看自己计算机侦听的端口：netstat -an

  测试远程计算机打开的端口：telnet xxx.xxx.xxx.xxx

* 可以**更改服务的端口**和**只打开必要的端口**来**增加服务器安全**

* Windows防火墙安全：

  * 在网卡上加一层过滤器：只允许某些端口的数据进来
  * 防火墙是单向的，只拦截来的，不管出去的，出去的时候自动打开端口，出去了就关了，外面的就不要想进来的。

* Windows防火墙程序防不了灰鸽子木马程序

  * 防火墙是严进宽出，灰鸽子利用了这一点，让中木马的计算机灰鸽子客户端主动连到服务端。所以防火墙防不了灰鸽子木马。

  * 解决灰鸽子木马的问题：用IPsec的网络层安全严格控制网络流量

## 运输层的功能

* TCP传送的协议数据的那元是TCP报文段

* UDP传送的协议数据单元是UDP报文或者用户数据报

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125125536.png" style="zoom:100%;margin-left:0px;" />

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125125723.png" style="zoom:80%;margin-left:0px;" />

## 运输层协议UDP和TCP

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125130057.png" style="zoom:80%;margin-left:0px;" />

  UDP是无连接的，即是发送数据之前不需要建立连接

  UDP使用尽最大努力交付数据，既不保证可靠交付，同时也不使用拥塞控制

  UDP是面向报文的，UDP没有拥塞控制，很适合多媒体通信的要求

  UDP支持一对一，一对多，多对一和多对多的叫交互通信

  UDP的首部开销小，只有8个字节

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125133249.png" style="zoom:67%;margin-left:0px;" />

  **TCP中的几个标志位的作用**

  * **URG** 紧急指针，告诉接收TCP模块**紧要指针域**指着紧要数据。
  * **ACK** 置1时表示**确认**号（为合法，为0的时候表示数据段不包含确认信息，确认号被忽略。
  * **PSH** 置1时请求的数据段在**接收方得到后就可直接送到应用程序**，而不必等到缓冲区满时才传送。
  * **RST** 置1时**重建连接**。如果接收到RST位时候，通常**发生了某些错误**。
  * **SYN** 置1时用来**发起一个连接**，建立通话和同意建立通话的标志。
  * **FIN** 置1时表示发端完成发送任务。用来**释放连接**，表明发送方已经没有数据发送了。

  TCP是面向连接的传输层协议

  每一条TCP连接只能有两个端点，每一条TCP连接只能是点对点的，这里的端点是应用程序，也就是套接字(IP+端口号)。

  TCP提供可靠交付的服务

  TCP提供全双工通信（表现为通信与反馈）

  面向字节流

## TCP可靠传输的实现

提供可靠传输，流量控制，拥塞控制功能

* 可靠传输的工作原理：停止等待，超时重传，确认丢失，确认迟到，自动重传

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125133753.png" style="zoom: 67%;margin-left:0px;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125133858.png" style="zoom:67%;margin-left:0px;" />

  使用ARQ机制的优点是简单，但是信道利用率太低（发送所用的时间占比太小）；基于此可以采用**流水线传输（连续ARQ协议），可以得到更高的信道利用率，采用滑动窗口设计实现流水线的可靠传输**

  发一个确认一个

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125134408.png" style="zoom: 67%;margin-left:0px;" />

  累计确认

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125134612.png" style="zoom:67%;margin-left:0px;" />

**netstat命令**用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况

```sh
-a (all)显示所有连接和监听端口选项，默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-o 显示与每个连接相关的所属进程 ID
-u (udp)仅显示udp相关选项
-n 以数字形式显示地址和端口号。
-l 仅列出有在 Listen (监听) 的服務状态
-p 显示 proto 指定的协议的连接
-r 显示路由信息，路由表
-e 显示以太网统计信息。此选项可以与 -s选项组合使用，例如uid等
-s 按各个协议进行统计
-c 每隔一个固定时间，执行该netstat命令。
提示：LISTEN和LISTENING的状态只有用-a或者-l才能看到
```

* **Wireshark抓包后的网络层次说明：**

  - **Frame:物理层**的数据帧概况
  - **Ethernet II: 数据链路层**以太网头部帧
  - **Internet Protocol Version 4：网络层**IP包头的信息
  - **Transmission Control Protocol：传输层**的数据段头部信息，此处是TCP协议。
  - **Hypertext Transfer Protocol：应用层**的信息，此处是**HTTP**协议

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125144521.png)

  ![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125144901.png)

## TCP协议连接管理：三次握手/四次挥手协议的大致执行流程

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125164801.png" style="zoom: 67%;margin-left:0px;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125164802.png" style="zoom:67%;margin-left:0px;" />

STP协议默认采用**滑动窗口累计确认**的机制：

**利用TCP的三次握手协议实现对XP系统的攻击：SYN攻击和LAND攻击**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125151050.png" style="zoom: 33%;margin-left:0px;" />

## TCP实际上的可靠传输实现：以字节为单位的滑动窗口

* **UDP的可靠传输**有上层的**应用层协议来实现**

* TCP传输过程中**无数据包丢失**

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125165726.png" style="zoom: 80%;margin-left:0px;" />

* TCP传输过程中**有数据包丢失**

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125165727.png" style="zoom: 80%;margin-left:0px;" />

**超时重传时间确认：**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125162814.png" style="zoom: 67%;margin-left:0px;" />

## TCP的流量控制

* TCP的流量控制是基于**滑动窗口机制**的

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125172256.png" style="zoom: 80%;margin-left:0px;" />

## TCP的拥塞控制

* TCP的拥塞控制指的是将网络中的**所有计算机**，是一种全局控制，是如何避免让网络产生网络堵塞的。当一个计算机发现有丢包问题出现时，会主动把发送数据包的速度降下来。

* 当资源的需求综合>可用的资源时，将产生资源拥塞。

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201125173907.png" style="zoom:80%;margin-left:0px;" />

* 拥塞控制的四种算法：慢开始，拥塞避免，快重传，快恢复

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127101954.png" style="zoom: 50%;margin-left:0px;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127101955.png" style="zoom:50%;margin-left:0px;" />

  A发送窗口的上限值=Min[A的拥塞窗口,B的接收窗口]

