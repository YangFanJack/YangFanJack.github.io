# 内网穿透浅析


**首先需要明确的是：内网穿透只是目的，不是特指哪一种技术，可以有多种方式来实现通过公网对局域网中的主机的访问。**

内网穿透的技术：目前了解的大致分为以下两种

* 基于client和server，利用公网的一个中间服务器实现流量转发
* 利用UDP打洞来实现P2P
* 当然，自己能申请公网ip更好。直接做一个端口映射（常见的端口转发有以下三种模式：手动设计端口转发规则，DMZ，UPNP）利用DDNS动态绑定域名即可。

Tips:其实，我觉得如果从实现目的这个层次去说，VPN的正向代理同样也实现了访问企业/学校内部局域网的目的，所以我觉得这也叫实现了内网穿透（不知道准不准确）

### 基于C/S架构流量转发实现（传统穿透）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201229113152.png" style="zoom: 67%;" />

在谈这种方式之前，需要先了解以下计算机网络中的Proxy，既代理。分为正向代理和反向代理

* **正向代理：** 隐藏了真实的请求客户端。服务端不知道真实的客户端是谁，客户端请求的服务都被代理服务器代替来请求，最典型的例子就是科学上网工具（访问谷歌被墙，于是在国外搭建一台代理服务器，让代理帮我去请求，代理把请求放回的数据再返回给我。此时，Proxy和Client同属一个LAN，对server透明）
* **反向代理：** 反向代理隐藏里真实的服务端，反向代理服务器会帮我们把请求转发到真实的服务器那里去。最典型的就是Nginx这个性能强大的反向代理服务器（当我们访问百度时，百度在全国分布了上万台服务器，你的每次请求具体是哪台服务器处理你的请求你不用知道，只需要Nginx这个反向代理服务器知道就行了，这实际就是负载均衡的实现原理。此时，Proxy和Server同属于一个LAN，对Client透明）

![反向代理&正向代理](https://img-blog.csdnimg.cn/20190715113403480.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyODQ4Nzcz,size_16,color_FFFFFF,t_70)

**正向代理：** 典型例子就是VPN和shadowsocks等代理上网工具，通过安装工具来让本地机器和代理服务器处于同一局域网
**反向代理：** 典型例子就是nginx和内网穿透，通过在服务端机器安装工具来让服务端机器和代理服务器处于同一局域网，nginx和内网穿透中的反向代理服务器的区别就是nginx可以根据算法策略自动选择服务终端，而内网穿透反向代理服务器直接通过工具和服务终端机做了ip:端口映射绑定

目前最常用的两个通过公网服务器转发数据实现内网穿透的工具是：**frp**，**ngrok**，**nat123**。远程桌面控制软件**TeamViewer**也是这样的原理。

**具体流程**如下：我们需要一个有公网ip的反向代理服务器C，由C代理本地的A主机（但在这个过程中，代理服务器C和本地A没有在一个网段，因此当公网B向代理服务器发送请求时，代理服务器无法连接到处于内网中的主机A。所以就需要上面提到的工具了，需要安装相应的服务或者客户端软件向Proxy主动发出请求并建立连接）。然后从本地A向C握手，建立稳定连接，同时在Proxy上将A的端口映射到公网IP上。之后，一个公网设备B向C发送请求，C通过刚才建立的稳定信道将请求转发给A。A收到请求后将响应发给C，C再将响应返给B。至此，穿透达成。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201229095308.png)

缺陷就是Proxy最为数据转发的唯一跳板，需要消耗服务器带宽和流量，内网穿透过程中的带宽上行取决于中间反向代理服务器。

### 利用UDP打洞实现P2P的访问（点对点穿透）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201229113151.png" style="zoom:67%;" />

简单说就是：用一个公网服务器当介绍人，直接连接两个不同局域网中的主机，来实现通讯，建立连接后把公网服务器关了，两个不同局域网中的机器依旧可以通信。

要弄清楚UDP打洞（NAT打洞）原理，必须先搞清楚NAT的4种类型

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201228094312.webp" style="zoom:80%;" />

* 先来看个例子：公网上有两个server，ip和端口分别如图。内网中的NAT网关的ip是3.3.3.3，NAT内部需要穿透的电脑的ip如图。此时，192.168.0.2电脑A，已经和NAT网关（3.3.3.3:200）和server1（1.1.1.1:1111）建立了连接

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201228095516.png" style="zoom:80%;" />

**以下四种方式从上至下的要求愈发严格：**

#### 完全锥形NAT

如上，当A和server1的一条通信连接建立时，**其他的所有server**，例如server2也可以通过路由上的3.3.3.3:200来访问电脑A

#### ip限制型锥形NAT

当A和server1的一条通信连接建立时，其他的server，例如server2不能通过路由上的3.3.3.3:200来访问电脑A，只能是此时正在正在连接当中的server1的**ip**才能访问电脑A，但是堆server1中的**不同端口号并不限制**

#### 端口限制型锥形NAT

当A和server1的一条通信连接建立时，其他的server，例如server2不能通过路由上的3.3.3.3:200来访问电脑A，只能是此时正在正在连接当中的server1的**ip+端口**才能访问电脑A

#### 对称NAT

当A和server1的一条通信连接建立时，**在完全满足端口映射锥形NAT的基础上**，如果此时电脑A还需要建立和其他server建立连接，比如server2，那么此时**不能重复利用3.3.3.3:200**，只能重新在路由器上开通一个端口号。

#### UDP打洞中不同NAT的组合方式

上述介绍四种NAT的类型对其进行两两组合，总共有10中不同的组合方式，不同的组合方式在进行穿透时的方法也不同，有两种情况的组合无法穿透（也有一些骚操作可以让这两种的组合实现内网穿透，但是由于NAT设计之初就没有考虑到这两种的组合情况，所以理论上他们也是不能穿透的）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201228102002.webp" style="zoom:80%;" />

### 两种内网穿透方式的比较

* 打洞：Zerotier，Frp（初级），smargate
* 转发：花生壳，ngrok，Frp

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201229113158.png" style="zoom: 50%;" />
