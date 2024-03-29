# 计网-数据链路层


<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122095537.png" style="zoom:50%;" />

## 数据链路层基本概念及基本问题

### 基本概念

* 两种信道类型：
  * 点到点信道
  * 广播信道
* 链路和数据链路：链路是物理层，数据链路包含物理层和数据链路层。链路是一条点到点的物理线路段，数据链路是除了物理线路外，还必须要有通信协议来控制这些数据的传输，这些协议的硬件和软件加到链路上就构成了数据链路
* 帧：在网络层包的基础上增加头和尾

### 三个基本问题

#### 封装成帧

* 在网络层IP数据报基础上+帧首部&帧尾部，帧的数据部分MTU≤1500Byte

#### 透明传输

* 用**字节填充法**解决透明传输问题

#### 差错控制

* 传输过程中可能会有比特差错（0变1或者1变0），误码率

* **循环冗余检验CRC**（除数由数据链路层协议确定）,通过CRC算法生成FCS帧检验序列

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122102828.png" style="zoom:50%;margin-left:0px;" /> 

  不纠错，有错误直接丢掉（重传不是数据链路层管的事情）

  有可能出错也检查不出，CRC不是可靠传输

## 两场情况下的数据链路层

### 使用点对点信道的数据链路层（PPP协议）

- 例子：拨号上网

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122103648.png" style="zoom: 80%;margin-left:0px;" />

- 特点：简单，封装成帧，支持多层网络协议，多种类型链路，差错检验，检测连接状态，最大传送单元，网络层地址协商，数据压缩协商

- 由三部分组成：

  * 一个将IP数据报封装到串行链路的方法；
  * 一个用来建立、配置和测试数据链路连接的链路控制协议LCP；
  * 一套网络控制协议NCP，其中的每一个协议支持不同的网络层协议

- PPP帧格式

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122105300.png" style="zoom:50%;margin-left:0px;" />

- 解决透明传输：传输比特（零比特填充法），传输字节（字节填充）

- <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122105257.png" style="zoom:50%;margin-left:0px;" />

- **PPP协议工作流程**

  * 当用户拨号接入ISP后，就建立了一条从用户PC机到ISP的物理连接。
  * 这时用户PC机向ISP发送一系列的LCP分组(封装成多个PPP帧)，以便建立LCP连接。
  * 这些分组及其响应选择了将要使用的一些PPP参数。
  * 接着还要进行网络层配置，NCP给新接入的用户PC机分配一个临时的IP地址。
  * 这样，用户PC机就成为因特网上的一个有IP地址的主机了。
  * 当用户通信完毕时，NCP释放网络层连接，收回原来分配出去的IP地址。
  * 接着，LCP释放数据链路层连接。
  * 最后释放的是物理层的连接。

- **PPPoE**:
  一种建立在以太网上的点到点的隧道技术。**以太网本身是基于广播的**，无法提供可靠的点到点支持，PPPoE解决了这个问题，在以太网上提供了可靠的点到点连接，可以让运营商验证你的身份并予以计费。

### 使用广播信道的数据链路层（以太网的CSMA/CD协议）

* 局域网的拓扑：星形网，总线网，树形网，环形网

* 共享通信媒体：

  * 静态划分信道：频分/时分/波分/码分复用
  * **动态媒体接入技术：随机接入（以太网）**，受控接入

* **多点接入，载波监听**

* **碰撞检测**，发现冲突最长时间是2焘 

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122141720.png" style="zoom:50%;margin-left:0px;" />

* **半双工通信**

* 2套=51.2微秒=1争用期=基本退避时间；10Mb/s的以太网争用期发送512bit（64Byte），前64字节没有发现冲突情况，之后也就不会有冲突了。所以**10M以太网中最短有效帧为64字节**。

* 退避算法

## 以太局域网

* 两个标准：Ethernet V2和802.3

* 用集线器组以太局域网

* 以太网信道利用率

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122143805.png" style="zoom:50%;margin-left:0px;" />

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122143808.png" style="zoom:80%;margin-left:0px;" />

* MAC层的硬件地址：48位二进制（前24为代表厂家，后24为厂家自己指定）

  * 网卡从网络上每收到一个MAC帧就会用硬件检查MAC帧中的MAC地址，如果是发往本站的帧则收下，其他的丢弃。包括单播帧，广播帧，多播帧
  * 一个局域网不能出现两个相同的mac地址，因为mac可以手动指定自定义的mac地址
  * <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122163027.png" style="zoom: 67%;margin-left:0px;" />
  * 帧间最小的间隔时间为9.6微秒，是为了使刚刚收到的数据帧站的接收缓存得以清理，做好接收下一帧的准备

## 扩展以太网

* 物理层的扩展

  * 用集线器连网最多不要超过30台机器，否则效率极慢

* 数据链路层的扩展

  * 网桥：有一个mac记忆表，可存储（来源）转发（目的），可以隔离冲突域
    * 透明网桥：以太网上的站点不知道所发送的帧将经过那几个网桥，是一种即插即用的设备——自学习
    * 源路由网桥：在发送帧时，把最详细的最佳路由信息（路由最少/时间最短）放在帧的首部中。（通过发送 ’ 发现帧 ‘ 来寻找最佳路由信息）
    
  * 交换机：可以看作多口网桥（网桥一般只有两个口，桥接隔离两个冲突域）。基本上每个口直接连一台机器，一台机器一个冲突域，也就是没有冲突了。可以实现全双工。

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201124125911.png" style="zoom:50%;margin-left:0px;" />

    **交换机的mac表的生成的流程：**

    - **A** **先向 B 发送一帧，从接口 1 进入到交换机。**
    - **交换机收到帧后，先查找交换表，没有查到应从哪个接口转发这个帧。**
    - **交换机把这个帧的源地址 A 和接口1 写入交换表中，并向除接口1以外的所有的接口广播这个帧。**
    - **C** **和 D 将丢弃这个帧，因为目的地址不对。只 B 才收下这个目的地址正确的帧。这也称为过滤。**
    - **从新写入交换表的项目 (A, 1) 可以看出，以后不管从哪一个接口收到帧，只要其目的地址是A，就应当把收到的帧从接口1转发出去。**
    - **B** **通过接口 3 向 A 发送一帧。**
    - **交换机查找交换表，发现交换表中的 MAC 地址有 A。表明要发送给A的帧（即目的地址为 A 的帧）应从接口1转发。于是就把这个帧传送到接口 1 转发给 A。显然，现在已经没有必要再广播收到的帧。**
    - **交换表这时新增加的项目 (B, 3)，表明今后如有发送给 B 的帧，就应当从接口 3 转发出去。**
    - **经过一段时间后，只要主机 C 和 D 也向其他主机发送帧，以太网交换机中的交换表就会把转发到 C 或 D 应当经过的接口号（2 或 4）写入到交换表中。**
    - **考虑到可能有时要在交换机的接口更换主机，或者主机要更换其网络适配器，这就需要更改交换表中的项目。为此，在交换表中每个项目都设有一定的有效时间。过期的项目就自动被删除。**

* 理解**冲突域和广播域**的区别（对于集线器，交换机，路由器）
  * 集线器工作在物理层,交换机工作在数据链路层。集线器不解码链路层帧,交换机可以解码链路层帧。集线器简单转发比特信号,交换机可以根据MAC寻址选择性转发。集线器不能隔离collisions domain,交换机可以隔离collisions domain,提高带宽利用率。

* 虚拟局域网：

  * LAN局域网：用集线器组成的一个网段就是一个LAN

  * VLAN虚拟局域网：用交换机组成的每个网段之间又可以组成新的一个个网段，就是虚拟局域网（相当于n刀把一个交换机分成n+1个交换机，也就由1张mac表变成了n+1张mac表）

    <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122183412.png" style="zoom:67%;margin-left:0px;" />

## 高速以太网

* 速率达到100Mb/s以上的以太网成为高速以太网
* 100BSSE-T以太网**全双工，不使用CSMA/CD协议**，因为可以在接口处排队
* 帧间时间间隔变为0.96微秒

## 半双工/全双工&集线器/交换机

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201122171509.jpg" style="zoom:80%;margin-left:0px;" />

* **集线器**只能实现**半双工**。**全双工**的网络必须要使用**交换机**组网。

* **集线器**，是共享带宽型网络设备，它本身没有数据处理能力，不能实现点到点的连接，所以不能实现全双工。虽然集线器可以使用双绞线，提供了两对线，但在任一时刻，只能有一对线工作，所以集线器只能是半双工的，不可能是全双工的。除非它带有部分交换机的功能。

* **交换机**，是独享带宽型网络设备，它本身有类似PC机CPU的数据交换处理器，能够识别连接到交换机各端口上的网络设备的MAC地址，能够实现点到点的专用连接，所以能够实现全双工操作。

* **注意：** 如果要实现全双工，交换机的端口不能和集线器相连，否则这个端口也只能工作在半双工状态下。交换机具有自动识别全双工和半双工状态的功能。(如上图)


