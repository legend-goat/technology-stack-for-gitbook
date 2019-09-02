# php 搭建实战

## PHP编译安装

环境：php5.6、Centos7.x

> 支持PHP7.x

### 准备

```text
# yum install gcc bison bison-devel zlib-devel libmcrypt-devel mcrypt mhash-devel openssl-devel libxml2-devel libcurl-devel bzip2-devel readline-devel libedit-devel sqlite-devel jemalloc jemalloc-devel
# cd /usr/local/src
# wget http://cn2.php.net/distributions/php-5.6.30.tar.gz
# tar zvxf php-5.6.30.tar.gz
# cd php-5.6.30
# groupadd www
# useradd -g www -s /sbin/nologin www
```

### 进入目录，配置

```text
# ./configure --prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--enable-inline-optimization --disable-debug \
--disable-rpath --enable-shared --enable-opcache \
--enable-fpm --with-fpm-user=www \
--with-fpm-group=www \
--with-mysql=mysqlnd \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-gettext \
--enable-mbstring \
--with-iconv \
--with-mcrypt \
--with-mhash \
--with-openssl \
--enable-bcmath \
--enable-soap \
--with-libxml-dir \
--enable-pcntl \
--enable-shmop \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-sockets \
--with-curl --with-zlib \
--enable-zip \
--with-bz2 \
--with-readline
```

### 编译&安装

```text
# make && make install
```

### 配置

```text
# cp php.ini-production /usr/local/php/etc/php.ini
# cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
# cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
# chmod +x /etc/init.d/php-fpm

# chkconfig --add php-fpm
# chkconfig on php-fpm
# service php-fpm start
```

### 配置环境变量

```text
# vim /etc/profile
export PATH=$PATH:/usr/local/php/bin:/usr/local/php/sbin

# source /etc/profile
```

## PHP扩展安装

环境：php5.6、Centos7.x

> 支持PHP7.x

### 准备

源码包下载，扩展在php源码ext/目录下。

### 编译安装

```text
## 调用已经编译好的php里面的
# phpize /usr/local/php/bin/phpize
# ./configure --with-php-config=/usr/local/php/bin/php-config
# make && make install
```

### 生效

编译之后，/usr/local/php/etc/php.ini 中打开扩展即可

