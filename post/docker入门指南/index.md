
# docker介绍

## 下载docker依赖环境

```sh
想安装Docker，需要先将依赖的环境全部下载下来，就像Maven依赖JDK一样
yum -y install yum-utils device-mapper-persistent-data lvm2
```

## 指定docker镜像源头

```sh
默认下载Docker会去国外服务器下载，速度较慢，可以设置为阿里云镜像源，速度更快
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 安装docker

```sh
yum makecache fast
yum -y install docker-ce
#配置阿里云镜像加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://46dcjg1g.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 启动docker

```sh
安装成功后，需要手动启动，设置为开机启动，并测试一下 Docker
#启动docker服务
systemctl start docker
#设置开机自动启动
systemctl enable docker
#测试
docker run hello-world
```

# docker中央仓库

```sh
1.Docker官方的中央仓库：这个仓库是镜像最全的，但是下载速度较慢。
https://hub.docker.com/
2.国内的镜像网站：网易蜂巢，daoCloud等，下载速度快，但是镜像相对不全。
https://c.163yun.com/hub#/home 
http://hub.daocloud.io/ （推荐使用）
3.在公司内部会采用私服的方式拉取镜像（添加配置）
#需要创建 /etc/docker/daemon.json，并添加如下内容
{
	"registry-mirrors":["https://registry.docker-cn.com"],
	"insecure-registries":["ip:port"]
}
#重启两个服务
systemctl daemon-reload
systemctl restart docker
```

# docker镜像操作

## 拉取镜像

```sh
从中央仓库拉取镜像到本地
docker pull 镜像名称[:tag]
#举个栗子:docker pull daocloud.io/library/tomcat:8.5.15-jre8
```

## 查看本地全部镜像

```sh
查看本地已经安装过的镜像信息，包含标识，名称，版本，更新时间，大小
docker images
```

## 删除本地镜像

```sh
镜像会占用磁盘空间，可以直接手动删除，标识通过查看获取
docker rmi 镜像的标识
```

## 镜像的拉入拉出

```sh
如果因为网络原因可以通过硬盘的方式传输镜像，虽然不规范，但是有效，但是这种方式导出的镜像名称和版本都是null，需要手动修改
#将本地的镜像导出
docker save -o 导出的路径 镜像id
#加载本地的镜像文件
docker load -i 镜像文件
#修改镜像文件
docker tag 镜像id 新镜像名称：版本
```

# 容器的操作

## 运行容器

```sh
运行容器需要定制具体镜像，如果镜像不存在，会直接下载
#简单操作
docker run 镜像的标识|镜像的名称[:tag]
#常用的参数
docker run -d -p 宿主机端口:容器端口 --name 容器名称 镜像的标识|镜像名称[:tag]
#-d:代表后台运行容器
#-p 宿主机端口:容器端口：为了映射当前Linux的端口和容器的端口
#--name 容器名称:指定容器的名称
```

## 查看正在运行的容器

```sh
查看全部正在运行的容器信息
docker ps [-qa]
#-a 查看全部的容器，包括没有运行
#-q 只查看容器的标识
```

## 查看容器日志

```sh
查看容器日志，以查看容器运行的信息
docker logs -f 容器id
#-f：可以滚动查看日志的最后几行
```

## 进入/退出容器内部

```sh
#进入容器内部
docker exec -t 容器id /bin/bash
#启动镜像并进入容器
docker run -it centos /bin/bash
#从容器停止并退出到宿主机
exit
#容器不停止并退出
Ctrl+P+Q
```

## 复制内容到容器

```sh
docker cp 宿主机路径 容器id:容器路径
```

## 重启&启动&停止&删除容器

```sh
容器的启动，停止，删除等操作，后续会经常使用到
#重新启动容器
docker restart 容器id
#启动停止运行的容器
docker start 容器id
 
#停止指定的容器(删除容器前，需要先停止容器)
docker stop 容器id
#停止全部容器
docker stop $(docker ps -qa)
#删除指定容器
docker rm 容器id
#删除全部容器
docker rm $(docker ps -qa)
```

# docker应用

## docker安装tomcat

```sh
运行Tomcat容器，为部署ssm工程做准备
docker search tomcat
docker run -d -p 8080:8080 --name tomcat \
-v $PWD:/usr/local/tomcat/webapps \
daocloud.io/library/tomcat:8.5.15-jre8
#或者已经下载了tomcat镜像
docker run -d -p 8080:8080 --name tomcat 镜像的标识
```

## 运行MySQL容器

```sh
docker search mysql
docker pull mysql:5.7.4
mkdir ~/mysql
cd ~/mysql
docker run -id -p 3307:3306 --name mysql \
-v $PWD/conf:/etc/mysql/conf.d \
-v $PWD/logs:/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=root \
daocloud.io/library/mysql:5.7.4
```

## 部署ssm工程

```sh
修改SSM工程环境，设置为Linux中Docker容器的信息
通过Maven的package重新打成war包
将Windows下的war包复制到Linux中
通过docker命令将宿主机的war包复制到容器内部
 
docker cp 文件名称 容器id:容器内部路径
 
测试访问SSM工程
```

## nginx
```sh
docker search nginx
docker pull nginx
mkdir ~/nginx
cd ~/nginx
mkdir conf
cd conf
vim nginx.conf
docker run -id --name=nginx \
-p 80:80 \
-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf
-v $PWD/logs:/var/log/nginx \
-v $PWD/html:/usr/share/nginx/html \
nginx
```

# docker常用命令

## 镜像命令

```sh
docker version #显示docker的版本信息
docker info    #显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help #帮助命令
docker images #查看所有镜像
	-a,--all #列出所有镜像
	-q,--quiet #只显示id
docker search XXX #搜索docker镜像
	--filter=STARS=3000 #通过搜藏来过滤
docker pull XXX[:版本号] #下载镜像(分层下载：docker的核心)
docker rmi -f 镜像id #删除指定镜像
docker rmi -f ${docker images -aq} #删除全部镜像
```

## 容器命令

```sh
#启动镜像新建容器并启动
docker pull centos
docker run centos #启动镜像新建容器
	--name="XXX" #用来区分容器
	-d #后台运行
	-it #使用交互容器运行
	-p #指定容器的端口
		-p 主机端口:容器端口（常用）
		-p ip:主机端口:容器端口
		-p 容器端口
		容器端口
	-P #随机指定端口
docker run -it centos /bin/bash #启动镜像并进入容器
exit #从容器停止并退出到宿主机
Ctrl+P+Q #容器不停止并退出
docker ps
	-a #列出当前正在运行的容器+历史运行过的容器
	-n=? #显示最近创建的容器
	-q #只显示容器的编号
docker rm 容器id
docker rm -f 容器id（不需要退出直接删除）
docker rm -f ${docker ps -aq} #删除所有容器	
#启动和停止容器
docker start 容器id #启动容器
docker restart 容器id #重启容器
docker stop 容器id #停止当前正在运行的容器
docker kill 容器id #强制停止当前容器
```

## 常用其他命令

```sh
# 容器要想后台运行必须要有相应的前台应用，因为docker容器仅在它的1号进程运行时会保持运行，如果1号进程推出了，容器也就退出了。
#日志
docker logs -f -t --tail 10 容器id
#查看容器中进程信息
docker top 容器id
#查看容器元数据信息
docker inspect 容器id
#进入当前正在运行的后台容器
docker exec -it 容器id /bin/bash（进入容器后开启一个新的终端，可以在里面交互）
docker attach 容器id（进入的是容器正在运行的命令行，不能交互）
# 从容器拷贝文件到宿主机
docker cp 容器id:容器内路径 目的宿主机路径
#拷贝是一个手动拷贝的过程，通过数据卷可以自动同步（类似于共享文件夹）
```

![](https://jack-blog-img.obs.cn-north-4.myhuaweicloud.com/github-page/img20201120092636.png)