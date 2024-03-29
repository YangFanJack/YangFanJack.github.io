# 计网-网络安全


## 网络安全问题概述

### 网络安全面临的4种威胁

* 截获（被动式攻击）：窃听他人通信内容
* 中断（主动式攻击）：中断他人的网络通信
* 篡改（主动式攻击）：故意修改网络上传送的报文
* 伪造（主动式攻击）：伪造信息在网络上传送

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127165620.png" style="zoom:80%;margin-left:0px;" />

**Cain**工具：**捕获同一网段**中用户输入的账号密码；篡改域名解析结果

* 通过arp欺骗实现（应对方法就是在自己的计算机上安装arp防火墙，但是如果交换机能监视，cain能直接从交换机拿数据，自己机器上的arp防火墙就没用了）

**DNS欺骗**：篡改DNS解析结果，比如钓鱼网站：你输入工商银行的域名，经由篡改服务机到DNS服务器得到正确的ip地址，回来先到篡改服务机，会把正确的ip改为一个和工商银行界面相似的钓鱼网站的ip，然后你进入钓鱼网站输入工商银行的域名，钓鱼网站就你的账号密码拿到了

**伪造**：一个服务器只允许某几台计算机能访问，这时一台范围外的计算机伪造自己的ip为其中一台有访问权限的ip来访问目标服务器，前提是那一台计算机没有运行，否则会出现ip冲突。

**中断**：Dos攻击和DDos攻击

### 计算机面临的威胁（恶意程序）

* 计算机病毒：熊猫烧香

  能传染，通过修改其他程序来吧自身或者变种复制进入完成的

* 计算机蠕虫：一般是消耗系统资源，不一定更改系统的设置

  通过网络的通信功能将自身从一个节点发送到另一个节点并启动运行的程序

* 特洛伊木马：盗号木马，灰鸽子远程控制木马，特点是需要和外界通信

  一种程序，执行的功能超出其所声称的功能

* 逻辑炸弹：定时错误

  一种当运行环境满足某种特定条件时执行其他特殊功能的程序

**如何养病毒**：需要把病毒文件加密，否则杀毒软件会清除掉这些文件。

## 两类密码体制

### 对称加密：

![对称加密模型](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/14236436-f27bd0f020d105b3.webp)

* 对称加密指的就是加密和解密使用同一个秘钥，所以叫做对称加密。对称加密只有一个秘钥，作为私钥。

* 常见的对称加密算法：DES（分组加密后在解密合并），AES，3DES等等。

* 缺点：

  * 密钥不能在网上传，只能本地保存，否则不安全

  * 密钥要两个人一对，密钥维护相当麻烦

* 优点：

  * 加密效率高

### 非对称加密：

![非对称加密模型](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/14236436-905efe6ee06ec3ba.webp)

* 非对称加密指的是：加密和解密使用不同的秘钥，一把作为公开的公钥，另一把作为私钥。公钥加密的信息，只有私钥才能解密。私钥加密的信息，只有公钥才能解密。
* 常见的非对称加密算法：RSA，ECC
* 公钥可以随便传，私钥需要本地保管。
* 非对称加密单独使用效率不如对称加密，所以**实际过程中的使用方法往往是非对称和对称的融合**：用对称加密加密传输内容，用非对称公钥加密加密对称加密的密钥，把这两部分一起发给接收方。接收方先通过非对称加密的私钥解密得到对称加密的密钥，用这个密钥再去解密得到传输内容。

对称加密算法相比非对称加密算法来说，加解密的效率要高得多。但是缺陷在于对于秘钥的管理上，以及在非安全信道中通讯时，密钥交换的安全性不能保障。所以在实际的网络环境中，会将两者混合使用.

## 数字签名

![数字签名模型](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/14236436-79c0d684fa37cfa0.webp)

* 数字签名是非对称加密的一个实际应用

* 作用：防止抵赖，能够检查签名之后内容是否被更改

* 目的不是为了保密，而是确保传输的文件没有被更改而且发送方无法狡辩说不是我发的（例如：中央红头文件发到地方政府）

* **图示：**

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127182819.png" style="zoom:67%;margin-left:0px;" />

* 注意观察上面的过程，有个细节问题：接收方如何确认解密的公钥是发送方发的。此时就需要第三方的证书颁发机构CA

## 因特网使用的安全协议

### 安全套接字层SSL

* 在应用层和传输层之间加入一个SSL加密层

* 在发送方，SSL接收应用层的数据，对数据进行加密，然后把加密的数据送往TCP套接字

* 在接收方，SSL从SSL套接字读取数据，解密后把数据交给应用层

* 例如：访问web时，不使用安全套接字的协议是http（80端口），使用了安全套接字的协议是https（443端口）

* 实现SSL需要的配置：也是用非对称密钥加密对称密钥来解决的（上面有介绍）

* 几个可以加上SSL层的应用层协议：

  ```sh
  IMAPS tcp-993
  POP3 tcp-995
  SMTPS tcp-465
  HTTPS tcp-443
  ```

* 其实SSL层可是实现如下三个功能

  * 加密SSL会话：（上面介绍的）
  * SSL服务器鉴别：允许用户证实服务器身份
  * SSL客户鉴别：允许服务器证实用户身份

### 网络层安全协议IPSec

网络安全是分层的：

* **数字签名/数字加密**：属于应用层安全，需要应用服务的支持加密解密
* **SSL层**：位于应用层和传输层之间，不需要应用程序的支持，但是需要配置证书才能实现SSL层安全
* **IPSec**：位于网络层(属于底层加密)，不需要应用程序的支持，也不需要配置任何证书，时自动加密的，对上层透明。

使用IPSec建立通信之前，两个节点之间需要建立一个SA安全关联

IPSec中的两个协议**AH**和**ESP**

* **AH**：只数字签名
* **ESP**：既要签名又要加密

## 链路加密与端到端加密

* 数据链路层的安全：每个网段传输数据加密解密

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127202702.png" style="zoom:67%;margin-left:0px;" />

* 数据链路层身份验证：PPP身份验证；ADSL数据链路层安全

## 防火墙

* 防火墙时由软件硬件构成的系统，是一种特殊编程的路由器，用来在两个网络之间实施接入控制策略。该策略由防火墙使用者自行制定以适合自身内网需要。
* 墙内的网络称为“可信赖的网络”，而将外部的因特网称为“不可信赖的网络”。
* 防火墙可以通过控制内网到Internet的数据流量来解决内联网和外联网的安全问题
* 可以分为：
  * 网络级防火墙：是基于数据包，源地址，目标地址，协议和端口，控制流量 来控制
  * 应用级防火墙：根据数据包，源地址，目标地址，协议，端口，用户名，时间段，内容来控制，可以防病毒进入内网
* 防火墙基于的结构有：
  * 边缘防火墙
  * 三向外围网
  * 背靠背防火墙
  * 单一网卡
