# Euler Server安装软件记录


# MySQL
1. MySQL官网下载RedHat版本的MySQL安装包：`https://dev.mysql.com/downloads/mysql/`
2. 按顺序安装：
```bash
rpm -ivh mysql-community-common-8.0.28-1.el8.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.28-1.el8.x86_64.rpm
rpm -ivh mysql-community-client-plugins-8.0.28-1.el8.x86_64.rpm
rpm -ivh mysql-community-client-8.0.28-1.el8.x86_64.rpm
rpm -ivh mysql-community-icu-data-files-8.0.28-1.el8.x86_64.rpm
rpm -ivh mysql-community-server-8.0.28-1.el8.x86_64.rpm

# 查看已安装的组件
rpm -qa | grep mysql
```
3. 初始化：
```bash
mysqld --initialize --user=mysql
```
4. 打开mysql服务
```bash
systemctl enable mysqld
systemctl start mysqld
```
5. 连接并修改密码
```bash
sudo grep 'temporary password' /var/log/mysqld.log
mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED BY '填写密码';
```
6. 激活远程登录
```bash
update user set host = '%' where user = 'root';
# 设置DBeaver的连接设置：修改AllowPublicKeyRetrieval=True
```
7. 创建新用户
```bash
CREATE USER 'linewell'@'%' IDENTIFIED BY '填写密码';
grant all privileges on 数据库名称.* to linewell@'%';
flush privileges;
```
8. my.cnf配置文件简介
[详细介绍](https://www.cnblogs.com/zhangweizhong/p/12179438.html)
若使用rpm方式安装，可能无/etc/my.cnf配置文件，需将/usr/share/mysql目录下配置文件复制至/etc目录下
```bash
cp /usr/share/mysql/my-large.cnf /etc/my.cnf
```

# Docker
1. 卸载旧版本
```bash
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
rm -rf /var/lib/docker

```
1. 给Euler增加源：`wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo`
2. 安装yum-utils
```bash
sudo yum install -y yum-utils
```
3. 添加docker包源
```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

vim /etc/yum.repos.d/docker-ce.repo
# 把对应 $releasever 修改成自己的Centos版本:8.0

```
5. 安装docker
```bash
# 安装最新版本
sudo yum install docker-ce docker-ce-cli containerd.io


# 如果安装特定版本，需要查询
yum list docker-ce --showduplicates | sort -r

docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable

#输入自己需要安装的版本
sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io

# 安装报了一个错误：Error：Problem: package docker-ce-3:23.0.1-1.el7.x86_64 requires containerd.io >= 1.6.4, but none of the providers can be installed

dnf install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.6.9-3.1.el7.x86_64.rpm
sudo yum install docker-ce docker-ce-cli

docker -v # 检查版本

systemctl enable docker # 开机启动
systemctl start docker # 启动引擎

docker run hello-world # 测试运行
# 但是执行后报错：
 Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: unable to apply apparmor profile: apparmor failed to apply profile: write /proc/self/attr/apparmor/exec: no such file or directory: unknown.
# 解决：centos的内核没有apparmor，所以这个问题可以通过忽略安全来绕过，结论就是安装docker最好使用debian系
docker run --security-opt apparmor=unconfined hello-world
```
