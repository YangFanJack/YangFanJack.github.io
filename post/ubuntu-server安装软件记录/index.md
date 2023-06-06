
# Debian系统安装后的一些配置

CentOS社区版的死亡，不成熟的EulerOS，导致目前选择红帽系的Linux发行版不是一个好主意。还是老老实实用Debian吧...

1. 配置软件源
```bash
vim /etc/apt/sources.list
deb http://repo.huaweicloud.com/ubuntu jammy main restricted
deb http://repo.huaweicloud.com/ubuntu jammy-updates main restricted
deb http://repo.huaweicloud.com/ubuntu jammy universe
deb http://repo.huaweicloud.com/ubuntu jammy-updates universe
deb http://repo.huaweicloud.com/ubuntu jammy multiverse
deb http://repo.huaweicloud.com/ubuntu jammy-updates multiverse
deb http://repo.huaweicloud.com/ubuntu jammy-backports main restricted universe multiverse
deb http://repo.huaweicloud.com/ubuntu jammy-security main restricted
deb http://repo.huaweicloud.com/ubuntu jammy-security universe
deb http://repo.huaweicloud.com/ubuntu jammy-security multiverse

apt upgrade
apt update
```
# Hugo部署
1. 安装git
```bash
apt install git
git config --global user.name "yangfanjack"
git config --global user.email "1144536063@qq.com"

# 创建git用户
adduser git
apt install sudo
chmod 740 /etc/sudoers
vim /etc/sudoers
root    ALL=(ALL)   ALL
git     ALL=(ALL)   ALL
chmod 400 /etc/sudoers
sudo passwd git
su - git
# 配置SSH认证，将本地计算机的SSH公匙加进去
mkdir ~/.ssh
vim ~/.ssh/authorized_keys #粘贴进去
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
#本机验证
ssh -v git@114.116.115.171

mkdir /home/git/blog/hugo
mkdir /home/git/blog/repo
cd /home/git/blog/repo
git init --bare hugo.git
# Git钩子配置
vim /home/git/blog/repo/hugo.git/hooks/post-receive
#!/bin/bash
git --work-tree=/home/git/blog/hugo --git-dir=/home/git/blog/repo/hugo.git checkout -f

sudo chown -R git:git /home/git/blog/repo/hugo.git/hooks/post-receive
sudo chmod +x /home/git/blog/repo/hugo.git/hooks/post-receive

# Nginx配置文件修改
user git
# 本地git项目修改
git init
git remote add origin git@114.116.115.171:/home/git/blog/repo/hugo.git

```


2. 安装nginx
```bash
apt install nginx
systemctl enable nginx
systemctl start nginx

# 修改/etc/nginx/nginx.conf
user git

# 修改/etc/nginx/sites-enabled/default
server {
        listen 80;
        listen [::]:80;
        # SSL configuration
        listen 443 ssl http2;
        listen [::]:443 ssl http2;

        server_name yangfanjack.top;
        root /home/git/blog/hugo;
        ssl_certificate "/etc/nginx/cert/server.crt";
        ssl_certificate_key "/etc/nginx/cert/server.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout 10m;
        ssl_ciphers DHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        error_page 404 /404.html;
                location = /40x.html{
        }

        error_page 500 502 503 504 /50x.html;
                location = /50x.html{
        }
        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;
}

# 华为云下载SSL证书放到服务器/etc/nginx/cert文件夹内
```
# 安装MySQL
```bash
apt update
apt install mysql-server
apt install mysql-client
mysql_secure_installation
# 上面命令如果设置密码陷入死循环，执行：
alter user 'root'@'localhost' identified with mysql_native_password by '密码';

systemctl status mysql.service
systemctl start mysql.service
systemctl enable mysql.service
systemctl disable mysql.service
systemctl stop mysql.service

# mysql的配置文件,开启远程登录
vim /etc/mysql/mysql.conf.d/mysqld.cnf
# 找到bind-address = 127.0.0.1这一行,改为bind-address = 0.0.0.0即可或简单一点注释掉也行，重启生效

# MySQL用户管理
# mysql8 初次安装后，需要先通过cat /var/log/mysqld.log | grep password 命令查看密码，修改密码时，需要 符合长度，且含有数字、小写或大写字母、特殊字符
mysql -uroot -p
alter user 'root'@'localhost' identified by '这里填你要的密码';
# 创建用户
# username：你将创建的用户名
# host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以从任意远程主机登陆，可以使用通配符%
# password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
# 远程登录
mysql -u test -h 115.28.203.224 -p
# 用户授权
GRANT privileges ON databasename.tablename TO 'username'@'host';
# 注意:用以上命令授权的用户不能给其它用户授权，如果想让被授权的用户可以将他的拥有的权限授给其他用户，用以下命令
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
# 刷新授权
flush privileges;
# 更改用户密码
ALTER USER 'username'@'host' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;  
# 撤销用户权限
REVOKE privileges ON databasename.tablename FROM 'username'@'host';
# 删除用户
DROP USER 'username'@'host';

# 查询用户表
use mysql;
show grants for 'username'@'host';
# 查看加密规则
select Host,User,plugin from mysql.user;
# 修改加密方式
ALTER USER 'username'@'host' IDENTIFIED WITH mysql_native_password BY 'password';


# 删除MySQL
systemctl stop mysql.service
apt remove --purge mysql-*
apt autoremove
# 查询是否有残余,如果还存在一些依赖，则继续用“apt remove 依赖包名称”命令删除；
dpkg --list | grep mysql
# 确认删除完整后，清理残余文件：
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
sudo rm -rf /etc/mysql
sudo rm -rf /var/lib/mysql
#最后用 dpkg -l | grep mysql 检查，如无返回即干净卸载
```
# 安装docker
[官方安装教程](https://docs.docker.com/engine/install/ubuntu/)
```bash
# 删除旧版本
apt remove docker docker-engine docker.io containerd runc
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
# 安装证书
apt update
apt install ca-certificates \
    curl \
    gnupg \
    lsb_release
# 添加Docker的官方GPG密钥：
mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 安装docker engine community
chmod a+r /etc/apt/keyrings/docker.gpg
apt update
apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
# 或者安装特定版本
apt-cache madison docker-ce
apt install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

# 配置阿里云镜像加速
vim /etc/docker/daemon.json
{
  "registry-mirrors": ["https://axvfsf7e.mirror.aliyuncs.com"]
}
systemctl daemon-reload
systemctl restart docker

# 测试Docker是否安装成功
docker run hello-world
```