# docker进阶指南


## docker可视化

```sh
# Portainer:利用Portainer可以轻松构建，管理及维护Docker环境，它基于容器安装，高效部署
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
# Rancher:Rancher是一个开源的企业级全栈化容器部署及管理平台。Rancher为容器提供一揽子基础架构服务：CNI兼容的网络服务、存储服务、主机管理、负载均衡、防护墙……Rancher让上述服务跨越公有云、私有云、虚拟机、物理机环境运行，真正实现一键式应用部署和管理。
docker run ‐di ‐‐name=rancher ‐p 9090:8080 rancher/server
```

## docker的分层机制

Docker镜像是**分层**构建的，Dockerfile 中每条指令都会新建一层。例如以下 Dockerfile:

```sh
FROM ubuntu:18.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

以上四条指令会创建四层，分别对应**基础镜像、复制文件、编译文件以及入口文件**，每层只记录本层所做的更改，而这些层都是**只读层(镜像层)**。当你启动一个容器，Docker 会在最顶部添加**读写层**，你在容器内做的所有更改，如写日志、修改、删除文件等，都保存到了读写层内，一般称该层为**容器层**，如下图所示：

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201120162027.jpeg)

事实上，**容器（container）和镜像（image）的最主要区别就是容器加上了顶层的读写层**。所有对容器的修改都发生在此层，镜像并不会被修改，也即前面说的 COW(copy-on-write)技术。容器需要读取某个文件时，直接从底部只读层去读即可，而如果需要修改某文件，则将该文件拷贝到顶部读写层进行修改，只读层保持不变。

**每个容器都有自己的读写层**，因此多个容器可以使用同一个镜像，另外容器被删除时，其对应的读写层也会被删除（如果你**希望多个容器共享或者持久化数据，可以使用 Docker volume**）。

最后，执行命令 docker ps -s，可以看到最后有两列 size 和 virtual size。其中 size就是容器读写层占用的磁盘空间，而 virtual size 就是读写层加上对应只读层所占用的磁盘空间。如果两个容器是从同一个镜像创建，那么只读层就是 100%共享，即使不是从同一镜像创建，其镜像仍然可能共享部分只读层（如一个镜像是基于另一个创建）。因此，docker 实际占用的磁盘空间远远小于 virtual size 的总和。

## commit镜像

```sh
docker commit #提交容器成为一个新的副本
docker commit -m="描述信息" -a="作者" 容器id 自定义目标镜像名:[版本号]
```



**接下来是docker的三个精髓：容器数据卷，DockerFile，Docker网络**

## 容器数据卷

用来实现"删服务不删数据"，数据可持久化，将**容器内的目录**映射**同步**到**宿主机的目录**中，类似于双向绑定

```sh
docker run -it -v 宿主机目录:容器内目录 #注意目录都是绝对地址
docker inspect 容器id #查看容器的具体同步目录信息Mount
#数据卷命令
docker volume create #创建数据卷
docker volume inspect #显示数据卷详细信息
docker volume ls #显示所有数据卷
docker volume rm #移除数据卷

#具名和匿名挂载（都不指定路径）
docker run -d -P name nginx02 -v juming-nginx:/etc/nginx nginx
docker run -d -P name nginx01 -v /etc/nginx ngix

所有的docker容器内的卷没有指定具体路径的情况下，都在`/var/lib/docker/volumes`下

-v /宿主机路径：容器内路径   #指定路径挂载
-v 数据卷名：容器内路径  #具名挂载
-v 容器内路径  #指定目录挂载

#这里的ro和rw代表读写权限，ro表示只读，rw表示读和写，对挂载出来的内容就进行了限制，ro表示只能通过宿主机进行改变，容器内无法操作
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx
```

```sh
#DockerFile:用来构建docker镜像的构建文件，相当于一个命令脚本。
#镜像是层次的，脚本里的命令也是层次的
#格式：指令(大写) 参数
#每个命令就是镜像的一层

FROM centos
VOLUME ["volume01","volume02"] #匿名挂载
CMD echo "------end-------"
CMD /bin/bash

docker build -f dockerfile01 -t jack/centos
```

```sh
#两个/多个容器之间同步数据（数据卷容器）
#docker02挂载到docker01上，此时docker01就是数据卷容器
docker run -d -it --name docker02 --volumes--from docker01 jack/centos
```

## DockerFile

```sh
#构建步骤
1. 编写dockerfile文件
2. docker build #构建成为一个镜像
3. docker run #运行镜像
4. docker push #发布镜像（dockerhub，阿里云镜像仓库）
```

```sh
#基础知识
1. 每个保留关键字（指令）都大写
2. 执行从上到下
3. "#"表示注释
4. 每一个指令都会创建提交到一个新的镜像层
```

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201120220311.png)

**dockerfile是面向开发的，交付环境就是交付一个dockerfile文件：DockFile->DockerImages->Docker容器**

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201120220723.png)

```sh
FROM #基础镜像
MAINTAINER #镜像是谁写的
RUN #镜像构建时需要运行的命令
ADD #添加内容（tocmat，mysql等）
WORKDIR #镜像的工作目录
VOLUME #挂载目录
EXPOSE #暴露的端口
CMD #指定容器启动的时候要运行的命令，只有最后一个会生效
ENTRYPOINT #指定容器启动的时候要运行的命令，可以追加命令
ONBUILD #只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行ONBUILD后的指令
COPY #将文件拷贝到目录中
ENV #构建的时候设置环境变量（例：mysql用户名）
```

**实战：自定义centos**

```sh
vim mydocker-centos
```

```sh
FROM centos
MAINTAINER jack<yangfanjack1024@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash
#ADD 
#VOLUME 
#ENTRYPOINT 
#ONBUILD 
#COPY 

```

```sh
#得到自定义镜像mycentos:0.1
docker build -f mydocker-centos -t mycentos:0.1 .
```

```sh
#编写包含tomcat的centos自定义镜像
1. 准备镜像文件tomvat和jdk的压缩包
2. 编写dockerfile文件，官方命名Dockerfile，build会自动寻找这个文件，就不需要-f指定了

#Dockerfile文件
FROM centos
MAINTAINER jack<yangfanjack1024@qq.com>
COPY readme.txt /usr/local/readme.txt

ADD jdk-8u271-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.40.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORK $MYPATH

#设置环境变量
ENV JAVA_HOME=/usr/local/jdk1.8.0_271
ENV CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.40
ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.40
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.40/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.40/bin/logs/catalina.out

#构建自定义tomcat的镜像
docker build -t diytomcat .
#启动tomcat
docker run -d -p 8080:8080 --name jacktomcat -v /root/test/:/usr/local/apache-tomcat-9.0.40/webapps/test/ -v /root/tomcatlogs/:/usr/local/apache-tomcat-9.0.40/logs/ diytomcat

#然后直接在宿主机的test中发布网站即可
```

**发布镜像到dockerhub和阿里云**

```sh
#先注册
https://hub.docker.com/
#在宿主机登录
docker login -u XXX -p XXX
#发布前先用tag命令修改为规范的镜像
docker tag 镜像id jack/diytomcat:1.0
docker push jack/diytomcat:1.0
#提交的时候就是按照层级来发布的
```

```sh
#先注册
https://www.aliyun.com/?spm=5176.12901015.ahjq0uaqt.1.a8fd525cv7uh0R
#找到容器镜像服务
#创建命名空间（一个账号只能创建三个）
#创建镜像仓库
#之后阿里云会有官方详细步骤
docker login XXXXXXXXXXXXXXX
docker tag XXXXXXXXXXXXX
docker push jack/diytomcat:1.0

```

**docker所有流程**

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121084949.png)

## Docker网络（容器编排，集群部署）

**docker网络模式**：有如下四种：

 host模式(--net=host)      container模式         none模式(--net=none)          bridge模式(--net=bridge)

**host模式**： 需要使用**docker run**是指定： **--net=host**   使用的网络实际上和宿主机是一样,在容器内的IP和宿主机的IP是一样,类似于**vmare桥接模式**；

container模式：使用 **--net=container:container_id/container_name**，多个容器使用共同的网络,看到的IP是一样的；

none模式： -**-net=none**  这种模式下,不会配置任何网络；

**bridge模式**：**--net=bridge**，不指定是默认也是这种模式，这种模式会为每个容器分配一个独立的network 网卡，同一个宿主机是在同一个网段下可以通信的，类似于 **VMware 的 NAT模式**；

**桥接模式**（类似于vm的nat模式）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121122657.png" style="zoom: 67%;" />

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121122811.png" style="zoom:50%;" />

**host模式**（类似于vm的桥接模式）

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121122812.png" style="zoom:50%;" />



**两个docker网络如何通信呢？**

* 一台bai计算机是可以有多个IP地址的，如果我们给一台du服务器绑定两个以上zhiIP地址dao，其中一个IP地址在一个网段中(如192.168.1.0)，另一个IP地址在另一个网段中(如192.168.2.0)，如果给服务器提供路由功能，这两个IP地址充当网关，就可以把两个网段连接在一起了。



**接下来是实战：Docker Compose，Docker Swarm，CI/CD Jenkins**

## DevOps

<img src="https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121112356.png" style="zoom:50%;" />

**DevOps（Development和Operations**的组合词）是一组过程、方法与系统的统称，用于促进开发（应用程序/软件工程）、技术运营和质量保障（QA）部门之间的沟通、协作与整合。

它是一种重视“软件开发人员（Dev）”和“IT运维技术人员（Ops）”之间沟通合作的文化、运动或惯例。透过自动化“软件交付”和“架构变更”的流程，来使得构建、测试、发布软件能够更加地快捷、频繁和可靠。

它的出现是由于软件行业日益清晰地认识到：为了按时交付软件产品和服务，开发和运维工作必须紧密合作。

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121111806.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121112357.png)

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201121112358.png)
