# nginx 搭建实战

## 基于单机的nginx编译安装

> 环境： centos7.x，nginx1.10

### 准备

```text
~# yum -y install gcc gcc-c++ autoconf automake
~# yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
~# wget http://nginx.org/download/nginx-1.10.0.tar.gz
~# tar -zxvf nginx-1.10.0.tar.gz
~# cd nginx-1.10.0
```

### 配置

```text
~# ./configure --prefix=/usr/local/nginx \
--conf-path=/usr/local/nginx/etc/nginx.conf \
--user=www \
--group=www
```

### 编译&安装

```text
~# make
~# make install
~# make clean
```

### 开机启动

```text
~# vim /usr/lib/systemd/system/nginx.service 

[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/etc/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/etc/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target

~# systemctl start nginx.service
~# systemctl enable nginx.service
```

### 加入环境变量

```text
~# vi /etc/profile
#末尾添加以下内容
#nginx env
export PATH=$PATH:/usr/local/nginx/sbin

# 生效
~# source /etc/profile
```

