# 计网-物理层


## 物理层的基本概念

* 物理层解决如何在连接各种计算机的传输媒体上传输数据比特流
* 主要任务时确定与传输媒体接口的一些特性
  * 机械特性：接口形状，大小，引脚数量
  * 电气特性：电压范围
  * 功能特性：例如规定-5V表示0，+5V表示1
  * 过程特性：连接时各个部件的工作步骤

## 数据通信的基础知识

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121195457.png)

* 消息：具有意义的数据

* 数据：运送消息的实体

* 信号：数据的电气或电磁的表现

  * 模拟信号：消息的参数取值时连续的

  * 数字信号：消息的参数取值是离散的

* 码元：在数字通信中常常用时间间隔相同的符号来表示一个二进制数字，这样的时间间隔内的信号称为二进制码元，而这个间隔被称为码元长度

* 码元长度：1码元可以携带的nbit信息量

* 信道：向一个方向传送消息的媒体（一条发送的信道+一条接收的信道）

  * 单工通信：单向高速公路
  * 半双工通信：对讲机
  * 全双工通信：电话

* 基带信号：来自信源的信号

* 带通信号：将基带信号经过载波调制（调幅，调频，调相）后（加频）

* 单极性不归零码，双极性不归零码，单极性归零码，双极性归零码，曼彻斯特编码，差分曼彻斯特编码

* 奈氏准则：在任何信道中，**码元传输速率是有上限**的，否则会出现码间串扰导致的干扰和失真，理想的信道的最高码元传输速率=2✖W(信道带宽:Hz) 波特

* 香农定理：信道的极限信息传输速率C=W✖log2(1+S/N) b/s
* 根据奈氏准则和香农公式可知：带宽BandWidth指的是频率宽度，单位是Hz。贷款大小并不直接对应传输速率快慢但对传输速率有决定性作用，带宽越大传输越快。所以在很多领域提及带宽指的是广义上的带宽（通信系统传输数据的能力），这个时候带宽单位就是bps了

## 物理层下面的传输媒体

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121202938.png)

* 导向传输媒体
  * 双绞线：STP，UTP
  * 同轴电缆：50Ω，75Ω
  * 光纤
* 非导向传输媒体
  * 无线电磁波传播（短波，微波）
* 物理层设备
  * 集线器：是一个很大的冲突域，采用广播的方式

## 信道复用技术

**充分利用一条共享信道**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121220043.png" style="zoom:67%;" />

### 频分复用技术（FDM）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121220341.png" style="zoom:50%;" />

### 时分复用技术（TDM）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121220518.png" style="zoom:67%;" />

### 统计时分复用（STDM）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121221519.png" style="zoom:67%;" />

### 码分复用技术（CDM）

**同一个频率同时传就只能采用码分复用**

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121223627.png" style="zoom: 80%;" />

## 数字传输系统

* PCM（脉码调制）：电话通信局用的，有两个标准（24路和32路）
* PCM提供模拟信号和数字信号之间的转换手段,支撑调制解调器模拟信号与数字信号的变换

## 宽带接入技术

* xDSL技术：用电话线提供internet接入。低频留给传统电话，高频给上网用，打电话和上网互不影响。采用频分复用技术

* HFC光纤同轴混合网：用有线电视电缆网加上光纤的上层改造提供internet接入。

* FTTx技术：光纤到户
