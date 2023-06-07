# 计网-无线网络


**无线网络的比较**

* PAN：蓝牙
* LAN：WiFi
* MAN：WiMAX
* WAN：2G，3G，4G，5G

以无线AP（无线交换机）为中心的无线局域网

最原始的单计算机拨号上网：

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127224828.png" style="zoom: 67%;margin-left:0px;" />

要实现一个局域网网段的多计算机同时上网，加入一个无线路由器来实现：

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127224833.png" style="zoom:67%;margin-left:0px;" />

通常所说的所谓“无线路由器”实际上是一个三合一设备：路由器功能+交换机功能+无线AP功能

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127224836.png" style="zoom:67%;margin-left:0px;" />

左边教室的所有机器按照上述能上网，右边教师的也想并入左边的网段上网，只需要把右边的无线路由器当作一个路由器来使就行了，拿一根网线把两个交换机连接起来，同时右边的无线设备也可以通过以无线AP为中心的无线局域网并入左边教室的网段。

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201127224910.png" style="zoom:67%;margin-left:0px;" />

**还有一种无线网络是不需要无线AP接入设备的：临时的无线网络**

手机通过3G,4G,5G来上网的原理：

* 手机打电话的方式：从最近的基站注册然后发出通信，基站之间通过光纤或者其他线连接。手机移动位置的时候需要连接新的基站，然后就会更新基站数据库
* 上网就是通过基站的光纤连接的网关来访问Internet，2G,3G,4G,5G，一代比一代的带宽高




