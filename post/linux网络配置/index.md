
## 网络命令

```sh
#配置eth0的IP地址， 同时激活该设备
sudo ifconfig eth0 192.168.1.10 netmask 255.255.255.0 up
#添加默认网关 
sudo route add default gw 192.168.1.1
#配置DNS
sudo vi /etc/resolv.conf
#激活设备
ifconfig eth0 up
#禁用设备
ifconfig eth0 down
```

## 网络配置

```sh
#查看相应设备名称对应的设备信息
ethtool eth0
#查看相应设备名称对应的设备位置(回车后与eth0 相对应的网卡接口旁边的指示灯就会闪烁，这样你就能很快确定eth0 网口的位置啦)
ethtool -p eth0
#Linux里面有两套管理网络连接的方案,两套方案是冲突的，不能同时共存。
	/etc/network/interfaces（/etc/init.d/networking）
	Network-Manager
```

**都是基于单网卡单ip的配置**

### 静态配置

#### Redhat类

```sh
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth[x]文件([x]为使用的网卡)
```

```sh
# xxxxxxx(网卡名称，不用改)
DEVICE=eth0
BOOTPROTO=static
TYPE=ether
HWADDR=xx:xx:xx:xx:xx:xx (网卡mac地址，不用改)
IPADDR=x.x.x.x(ip地址)
NETMASK=x.x.x.x(子网掩码)
BROADCAST=x.x.x.x.(广播地址)
NETWORK=x.x.x.x(网络地址)
GATEWAY=x.x.x.x(网关地址)
ONBOOT=yes(开机自启动)
DNS1=x.x.x.x(域名服务器地址)
DNS2=x.x.x.x 
```

#### Ubuntu类

```sh
sudo vim /etc/network/interfaces
```

```sh
# The primary network interface (配置主网络接口)
　　auto eth0 #开机自动激活eth0接口
　　iface eth0 inet static #配置eth0接口为静态地址
　　address 192.168.1.10
　　gateway 192.168.1.254
　　Netmask 255.255.255.0
　　network 192.168.1.0
　　broadcast 192.168.1.255 
```

### 动态配置

#### Redhat类

```sh
# xxxxxxx(网卡名称，不用改)
DEVICE=eth0
BOOTPROTO=dhcp
TYPE=ether
HWADDR=xx:xx:xx:xx:xx:xx (网卡mac地址，不用改)
ONBOOT=yes(开机自启动) 
```

#### Ubuntu类

```sh
# The loopback network interface (配置环回口)
　　auto lo # 开机自动激lo接口
　　iface lo inet loopback # 配置lo接口为环回口
　　# The primary network interface #配置主网络接口
　　auto eth0 #开机自动激活eth0接口
　　iface eth0 inet dhcp #配置eth0接口为DHCP自动获取
```

## VM的三种网络模式

* 打开vmware虚拟机，我们可以在选项栏的“编辑”下的“虚拟网络编辑器”中看到VMnet0（桥接模式）、VMnet1（仅主机模式）、VMnet8（NAT模式），那么这些都是有什么作用呢？其实，我们现在看到的VMnet0表示的是用于桥接模式下的虚拟交换机；VMnet1表示的是用于仅主机模式下的虚拟交换机；VMnet8表示的是用于NAT模式下的虚拟交换机。

* 同时，在主机上对应的有VMware Network Adapter VMnet1和VMware Network Adapter VMnet8两块虚拟网卡，它们分别作用于仅主机模式与NAT模式下。在“网络连接”中我们可以看到这两块虚拟网卡，如果将这两块卸载了，可以在vmware的“编辑”下的“虚拟网络编辑器”中点击“还原默认设置”，可重新将虚拟网卡还原。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224626.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224633.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20220521224641.png)