# php 搭建实战

## 基于单机的PHP及扩展编译安装

> 环境：php5.6、Centos7.x （支持PHP7.x）

### PHP编译安装

#### 1、准备

```text
~$ yum install gcc bison bison-devel zlib-devel libmcrypt-devel mcrypt mhash-devel openssl-devel libxml2-devel libcurl-devel bzip2-devel readline-devel libedit-devel sqlite-devel jemalloc jemalloc-devel
~$ cd /usr/local/src
~$ wget http://cn2.php.net/distributions/php-5.6.30.tar.gz
~$ tar zvxf php-5.6.30.tar.gz
~$ cd php-5.6.30
~$ groupadd www
~$ useradd -g www -s /sbin/nologin www
```

#### 2、进入目录，配置

```text
~$ ./configure --prefix=/usr/local/php \
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

#### 3、编译&安装

```text
~$ make && make install
```

#### 4、配置

```text
~$ cp php.ini-production /usr/local/php/etc/php.ini
~$ cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf
~$ cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
~$ chmod +x /etc/init.d/php-fpm

~$ chkconfig --add php-fpm
~$ chkconfig on php-fpm
~$ service php-fpm start
```

#### 5、配置环境变量

```text
~$ vim /etc/profile
export PATH=$PATH:/usr/local/php/bin:/usr/local/php/sbin

~$ source /etc/profile
```

### PHP扩展安装

> 环境：php5.6、Centos7.x （支持PHP7.x）

#### 1、准备

源码包下载，扩展在php源码ext/目录下。

#### 2、编译安装

```text
## 调用已经编译好的php里面的phpize
~$ /usr/local/php/bin/phpize
~$ ./configure --with-php-config=/usr/local/php/bin/php-config
~$ make && make install
```

#### 3、生效

编译之后，/usr/local/php/etc/php.ini 中打开扩展即可

### 调试工具 xdbug 安装

#### 1、xdebug zend扩展安装

扩展安装不再赘述。安装后，开启xdebug及相关配置。

```text
[XDebug]
zend_extension = /usr/local/php56/lib/php/20131226/xdebug.so
xdebug.trace_output_dir = '/mnt/shared/xdebugdir'
xdebug.profiler_output_dir = '/mnt/shared/xdebugdir'
xdebug.profiler_output_name = 'cachegrind.out.%t.%R'
xdebug.trace_output_name = 'xdebug.trace.%t.%R'
xdebug.auto_trace = on
xdebug.auto_profile = on
xdebug.profiler_enable = on
xdebug.trace_enable_trigger=1
xdebug.show_mem_delta=1
xdebug.collect_params=4
xdebug.collect_return=1
xdebug.trace_format=1
```

#### 2、查看工具

调用树跟踪（trace）可以使用 [xdebug-trace-viewer](https://github.com/kuun/xdebug-trace-viewer) 。

性能分析（profile）可以使用 [WinCacheGrind](https://sourceforge.net/projects/wincachegrind/)。

**参考资料：**

* [PHP 调试技术手册](http://blog.xiayf.cn/assets/uploads/files/PHP-Debug-Manual-public.pdf) 
* [xdebug 官方文档](https://xdebug.org/docs/all_settings)



