---
title: Web 项目线上部署
date: 2019-01-06 18:40:00
author: lt
img: https://upload-images.jianshu.io/upload_images/12625950-e1eef05d1c88ea23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 # 或者:/source/images/xxx.jpg
top: false # 如果top值为true，则会是首页推荐文章
# 如果要对文章设置阅读验证密码的话，就可以在设置password的值，该值必须是用SHA256加密后的密码，防止被他人识破
# password: 8d969eef6ecad3c29a3a629280e686cf0c3f5d5a86aff3ca12020c923adc6c92
# 本文章是否开启mathjax，且需要在主题的_config.yml文件中也需要开启才行
mathjax: false
categories: Web知识
tags:
  - web
  - linux
  - 项目实战
---

OS：CentOS 7.3 64 位

##### 设置阿里云镜像

根据帮助，进行备份、设置、生成缓存。
https://opsx.alibaba.com/mirror?lang=zh-CN

##### 创建用户

不用 root 用户进行操作，否则会不安全。

``` bash
useradd -d /usr/lee -m lee    # -d -m 创建 /usr/lee 目录
cd /usr/lee/
passwd lee    # 重置密码
sudo vim /etc/sudoers    # 给予该用户 sudo 权限
# 在文件中 /root 找到 root
# :noh 取消 vim 中的高亮
# 在 root 那一行下面输入： lee All=(ALL)  ALL，然后 wq!
exit    # 退出服务器，然后用 lee 重新连接服务器
```

##### 环境配置

``` bash
# --------jdk 安装--------
rpm -qa | grep jdk    # 检查本地 jdk 安装情况，安装了使用 yum remove 删掉

# --------安装过程--------
cd /
sudo mkdir developer
cd developer/
sudo mkdir setup
cd setup/
sudo wget 站点/jdk**.rpm
ll    # 查看文件权限
sudo chmod 777 jdk**.rpm
sudo rpm -ivh jdk**.rpm    # 默认路径安装

cd /usr/java/jdk**/    # jdk 默认安装路径
cd ..

# --------环境变量配置--------
sudo vim /etc/profile
# 复制下文引用中的文本到文件的最下边
source profile    # 使配置生效
```

``` bash
# --------Tomcat 安装--------
cd developer
wget 路径/tomcat**.tar.gz
sudo tar -zxvf tomcat**.tar.gz
sudo mv tomcat**.tar.gz setup/
cd tomcat**    # 进入 tomcat 文件夹
sudo vim conf/server.xml    # 修改字符集为 utf-8
# 输入 /8080，:noh，如下修改后 wq -->
# redirectPort="8443" URIEncoding="UTF-8"/>

cd bin
sudo ./startup.sh    # 此时安全组策略和防火墙都是空的，外部可以访问到 tomcat
# 如果无法访问 8080 端口，则需要配置云服务器的安全组，开放 8080 端口
```

``` bash
# --------Maven 安装--------
cd developer
wget 路径/maven**.tar.gz
sudo tar -zxvf maven**.tar.gz
sudo mv maven**.tar.gz setup/
```

> profile 中需要配置的环境变量，注意修改版本号
> ``` bash
>export JAVA_HOME=/usr/java/jdk1.8.0_191-amd64
>export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
>export MAVEN_HOME=/developer/apache-maven-3.6.0
>export NODE_HOME=/usr/local/node-v4.4.7-linux-x64
>export RUBY_HOME=/usr/local/ruby
>export CATALINA_HOME=/developer/apache-tomcat-7.0.92
>
>export PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin:$MAVEN_HOME/bin:$NODE_HOME/bin:/usr/local/bin:$RUBY_HOME/bin
>
>export LC_ALL=en_US.UTF-8
>```

``` bash
# --------vsftpd--------vsftpd 和 nginx 都要配置防火墙，都配置完后再配置防火墙
sudo yum -y install vsftpd    # -y 对所有提问都回答 yes

cd /
mkdir product
cd product/
sudo mkdir ftpfile
ll    # 可以看到 ftpfile 文件夹属于 root
sudo useradd ftpuser -d /product/ftpfile -s /sbin/nologin    # 创建该文件的匿名用户，-d 表示用户所属默认目录，-s 指明默认的 shell

sudo chown -R ftpuser.ftpuser ./ftpfile/
sudo passwd ftpuser
cd /etc/vsftpd
sudo vim chroot_list     # 添加前面的匿名用户 ftpuser，保存即可
sudo vim /etc/selinux/config    # 确保 SELINUX=disable
sudo setsebool -P ftp_home_dir 1    # 避免 550 拒绝访问错误, is disable
sudo mv vsftpd.conf vsftpd.conf.bak
sudo wget http://learning.happymmall.com/vsftpdconfig/vsftpd.conf

# 重启
systemctl vsftpd restart
# 查看vsftpd服务的状态
systemctl status vsftpd.service
# 设置开机启动
systemctl enable vsftpd.service

cd /product/ftpfile/
sudo mkdir img
sudo chown ftpuser img/
sudo chgrp ftpuser img/    # 组
sudo chmod g+w img/

# --------调试--------
# ftp 一直没办法登录，也没办法上传图片
# 修改 /etc/pam.d/vsftpd    将 auth required pam_shells.so 修改为 -> auth required pam_nologin.so
# 修改 /etc/vsftpd/vsftpd.conf    加入 "allow_writeable_chroot=YES"
```

``` bash
# --------Nginx 安装--------
cd /developer/
cd setup/
sudo wget http://nginx.org/download/nginx-1.14.2.tar.gz# nginx 是需要编译的，所以下载到哪里都可以

# --------安装 nginx 的依赖--------
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

# --------解压编译--------
sudo tar -zxvf nginx**.tar.gz
cd nginx**
sudo ./configure    # 使用了默认的路径
sudo make
sudo make install

# --------配置--------
whereis nginx    # 查找 nginx 的路径
cd /usr/local/nginx/
cd conf
sudo vim nginx.conf    # 编辑主文件
# 在 HTTPS server 上一行加入：include vhost/*.conf;
sudo mkdir vhost
cd vhost/

sudo wget http://learning.happymmall.com/nginx/linux_conf/vhost/admin.happymmall.com.conf
sudo wget http://learning.happymmall.com/nginx/linux_conf/vhost/happymmall.com.conf
sudo wget http://learning.happymmall.com/nginx/linux_conf/vhost/img.happymmall.com.conf
sudo wget http://learning.happymmall.com/nginx/linux_conf/vhost/s.happymmall.com.conf
cd ..
cd ..
cd sbin/
sudo ./nginx
```

``` bash
# --------MariaDb--------
sudo yum -y install mariadb-server mariadb mariadb-client mariadb-devel
sudo systemctl start mariadb    # 启动
sudo systemctl enable mariadb    # 开机启动
sudo systemctl stop mariadb    # 关闭
sudo systemctl restart mariadb    # 重启
# 字符集设置成 utf8
mysql -u root    # 进入 mysql

# --------数据库操作--------
mysql> select user, host, password from mysql.user;

mysql> set password for root@localhost = password('rootpassword');    # 修改本地 root 账户的密码
mysql> set password for root@云服务器账户名 = password('rootpassword');    # 修改本地 root 账户的密码
mysql> set password for root@127.0.0.1 = password('rootpassword');    # 修改本地 root 账户的密码
mysql> set password for 'root'@'::1' = password('rootpassword');    # 好像是 cmd 方式访问的密码
mysql> exit

mysql -u root -p

mysql> delete from mysql.user where user='';
mysql> flush privileges;    # 刷新

mysql> insert into mysql.user(host, user, password) values("localhost", "lee", password("leepassword@"));    # 因为是本地操作数据库，因此不需要开放 3306 端口
mysql> create database `mmall` default character set utf8 COLLATE utf8_general_ci;
mysql> flush privileges;
mysql> grant all privileges on mmall.* to lee@localhost identified by 'leepassword@';
mysql> exit

# --------数据导入--------
cd /developer/
sudo wget http://learning.happymmall.com/mmall.sql
mysql -u root -p

mysql> show databases;
mysql> use mmall;
mysql> show tables;
mysql> source /developer/mmall.sql
mysql> select * from mmall_user\G;    # \G 进行格式化
```

``` bash
# --------Git 安装--------
cd /developer/setup/
sudo wget 路径/git**.tar.gz
sudo tar -zxvf git**.tar.gz
cd git**
sudo make prefix=/usr/local/git all
sudo make prefix=/usr/local/git install
whereis git
sudo vim /etc/profile    # PATH 中加入 /usr/local/git/bin 路径
source /etc/profile

# --------Git 配置--------
git config --global user.name "thomasleedev"
git config --global user.email "thomaslee.dev@gmail.com"
git config --global core.autocrlf false    # 使 linux 和 windows 换行符转换
git config --global core.quotepath off    # 避免中文乱码
git config --global gui.encoding utf-8
ssh-keygen -t rsa -C "thomaslee.dev@gmail.com"
ssh-add ~/.ssh/id_rsa
# 如果执行不成功
eval `ssh-agent`
ssh-add ~/.ssh/id_rsa

cat ~/.ssh/id_rsa.pub    # 把公钥放到 GitHub 就好了
```

```
# --------防火墙配置--------
cd /etc/sysconfig/
ll | grep ipt    # 查看是否有 iptables -> 没有

# --------初始化防火墙--------
sudo iptables -P OUTPUT ACCEPT
# 之后会拿线上防火墙替换
sudo systemctl disable firewalld
sudo yum install iptables-services
sudo systemctl enable iptables
sudo service iptables save
ll | grep ipt
sudo mv iptables iptables.bak
ll | grep ipt
sudo wget http://learning.happymmall.com/env/iptables
sudo vim iptables
# 注释掉 3306、5005（Tomcat 远程端口）、8080 三个端口
sudo systemctl restart iptables    # 重启防火墙
```

##### 自动化发布

``` bash
# --------Maven + Git + --------
cd /developer
sudo wget http://learning.happymmall.com/deploy/deploy.sh
sudo vim deploy.sh

sudo mkdir git-repository

# --------给予当前用户操作 developer 权限--------
cd /
sudo chown -R lee /developer
sudo chmod u+w -R /developer
sudo chmod u+r -R /developer
sudo chmod u+x -R /developer

cd /developer/git-repository
git clone git@...    # 此时不需要加 sudo 了

cd ..
sudo vim deploy.sh
# 修改成当前的 git 目录 eg. /developer/git-repository/leemall
# 修改 war 包那一行的路径成 git 类似, checkout 切换的分支要和 Github 保持一致

./deploy.sh

# 部署后查看日志
cd tomcat**
tailf logs/catalina.out
```
