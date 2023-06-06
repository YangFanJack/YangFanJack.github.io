
## 域名系统DNS

* QQ客户端能登录，但是QQ网站打不开，可能说明DNS出问题了

* 域名服务器采用分布式的域名查询解析

  <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127112636.png" style="zoom: 67%;margin-left:0px;" />

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127112635.png" style="zoom: 67%;margin-left:0px;" />

* 我个人认为这里的**本机缓存**指的是本机的DNS缓存，访问网站的时候是先查询本机缓存，再查询**hosts配置**，之后才去请求**本地DNS缓存**。

## 动态主机配置协议DHCP

* 静态IP地址：手动配置

  一般固定的台式机

* 动态IP地址：通过DHCP协议自动获得

  一般的移动的设备，例如wifi连接的笔记本电脑

  * 计算机通过广播向DHCP服务器发送广播请求，DHCP服务器从地址池里选择一个地址给计算机发过去。如果同时有多个DHCP服务器应答，计算机还要选择一个DHCP服务器发一个确认信息，然后DHCP服务器才能把所有设置给计算机。
  * DHCP服务器要是静态地址，一般只给本网段的计算机分配地址。
  * 了解租期和续约
  * 除了同网段的DHCP分配，还可以跨网段DHCP分配

* **对比RARP** 逆向地址解析协议：从功能上说，RARP只能实现简单的从MAC地址到IP地址的查询工作，RARP server上的MAC地址和IP地址是必须事先静态配置好的。但DHCP却可以实现除静态分配外的动态IP地址分配以及IP地址租期管理等等相对复杂的功能。

* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127140344.png" style="zoom:80%;margin-left:0px;" />

## 文件传送协议FTP

* 控制连接：标准端口21，用于发送ftp命令信息
* 数据连接：标准端口22，用于上传下载数据
* 数据连接的建立类型：
  * 主动模式：ftp客户端告诉ftp服务器使用什么端口侦听，ftp服务器和ftp客户端的这个端口建立连接
  * 被动模式：ftp服务器打开一个端口等待客户端来连接
* FTP服务器端如果有防火墙，需要在防火墙上开21和20端口，需要使用主动模式进行连接

## 远程终端协议TELNET

* 默认使用TCP 的23端口
* <img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127142955.png" style="zoom:50%;margin-left:0px;" />

## 远程桌面RDP

* net user administrator all 更改用户密码
* net user han all /add 添加用户
* 将用户添加到远程桌面组 Remote Desktop Users组
* Server多用户操作系统，启用远程桌面可以都用户同时使用服务器
* XP和Windows 7是单用户操作系统，不支持多用户同时登录
* 远程时可以将本地的硬盘资源映射到远程计算机上

## 万维网WWW

* 一个网站的标志：可以通过不同端口，不同的IP地址，不同的主机头（域名）来区分；
  * tomcat服务器部署了多个项目，阿里云的域名解析到服务器ip，可以通过配置，让不同域名解析访问不同的项目。

### http协议

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127154738.png" style="zoom:100%;margin-left:0px;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127154742.png" style="zoom:80%;margin-left:0px;" />

### web代理服务器

1. 节省内网访问Internet的带宽
2. 使用代理服务器连网关，DNS都不需要，只需要和代理的服务器通信就行了，所有访问Internet的工作都是代理服务器做的。
3. 能绕过防火墙直接访问墙外的网站，这是一种正向代理

## 电子邮件（SMTP，POP3，IMAP）

### 发送协议：SMTP

### 接收邮件协议：POP3，IMAP

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127160235.png" style="zoom: 50%;margin-left:0px;" />



**注意：**

DNS占用53号端口，同时使用TCP和UDP协议。DNS在**区域传输**的时候使用TCP协议，**其他时候**使用UDP协议。

**DNS区域传输的时候使用TCP协议：**

1.辅域名服务器会定时（一般3小时）向主域名服务器进行查询以便了解数据是否有变动。如有变动，会执行一次区域传送，进行数据同步。区域传送使用TCP而不是UDP，因为数据同步传送的数据量比一个请求应答的数据量要多得多。

2.TCP是一种可靠连接，保证了数据的准确性。

**域名解析时使用UDP协议：**

客户端向DNS服务器查询域名，一般返回的内容都不超过512字节，用UDP传输即可。不用经过三次握手，这样DNS服务器负载更低，响应更快。理论上说，客户端也可以指定向DNS服务器查询时用TCP，但事实上，很多DNS服务器进行配置的时候，仅支持UDP查询包。