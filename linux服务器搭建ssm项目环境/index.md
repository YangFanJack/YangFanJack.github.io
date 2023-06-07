# linux服务器搭建ssm项目环境


### 购买服务器

阿里云云翼计划：9.5块钱/月

### 配置环境

#### jdk环境

```sh
yum -y install java-1.8.0-openjdk.x86_64
java -version
```

#### mysql环境

* 安装mysql

```sh
cd /tmp
wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm  
rpm -ivh mysql-community-release-el7-5.noarch.rpm 
yum install mysql mysql-server mysql-devel -y
```

* 启动mysql

```sh
systemctl start mysql.service
mysqladmin -u root password root
mysql -uroot -proot
```

#### tomcat环境

* windwos环境中下载tomcat9的tar.gz后缀的压缩包

  https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/

* 用FileZilla上传到linux服务器`/usr/local`目录下

* 解压缩后启动tomcat

  ```sh
  cd /usr/local
  tar xzf apache-tomcat-9.0.17.tar.gz
  #更名文件夹为tomcat9
  cd /usr/local/tomcat9/bin
  ./startup.sh
  ```

### 打包上传war包

* 打包上传前需要在阿里云的服务器防火墙中打开8080端口权限

  也可以把tomcat的默认款口号设置为80（在tomcat的conf目录下修改server.xml文件的默认端口号）（推荐）

* 在IDEA中用maven工具先clean后package，在target目录下生成war包，用filezilla上传到tomcat的webapps目录下。

* 重启tomcat

然后就可以访问你自己的网站了。
