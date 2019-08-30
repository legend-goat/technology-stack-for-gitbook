# mysql 搭建实战

## 编译安装

**环境**： Centos7.x、mysql5.7

### 准备

```text
# yum -y install gcc gcc-c++ ncurses ncurses-devel cmake bison bison-devel

# cd /usr/local/src

# wget http://www.sourceforge.net/projects/boost/files/boost/1.59.0/boost_1_59_0.tar.gz
# tar -xvzf boost_1_59_0.tar.gz

# wget http://101.96.10.47/dev.mysql.com/get/Downloads/MySQL-5.7/mysql-boost-5.7.14.tar.gz
# tar -zvxf mysql-boost-5.7.14.tar.gz

# mv boost_1_59_0 mysql-5.7.14
# cd mysql-5.7.14

# groupadd -r mysql
# useradd -r -g mysql mysql

# mkdir -p /home/mysql/data
# mkdir -p /home/mysql/logs
# mkdir -p /home/mysql/temp
```

### 进入目录，配置

```text
# cmake  
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql \
-DMYSQL_DATADIR=/home/mysql/data \
-DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock \
-DWITH_BOOST=./boost_1_59_0 \
-DSYSCONFDIR=/etc \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLE_DTRACE=0 \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DWITH_EMBEDDED_SERVER=1 
```

### 编译&安装

```text
# make
# make install
# make clean
```

### 开机启动

```text
# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
# chmod +x /etc/init.d/mysqld 
# systemctl enable mysqld
```

### 目录权限

```text
# chown -Rf mysql:mysql /usr/local/mysql
# chown -Rf mysql:mysql /home/mysql
```

### 添加环境变量

```text
# vi /etc/profile
# 末尾添加以下内容
# mysql env
export PATH=$PATH:/usr/local/mysql/bin:/usr/local/mysql/lib

# 生效
# source /etc/profile
```

### 配置

```text
# vi /etc/my.cnf

[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci

skip-external-locking
skip-name-resolve

user = mysql
port = 3306

basedir = /usr/local/mysql
datadir = /home/mysql/data
tmpdir = /home/mysql/temp
# server_id = .....
socket = /usr/local/mysql/mysql.sock
log-error = /home/mysql/logs/mysql_error.log
pid-file = /home/mysql/data/mysql.pid

open_files_limit = 10240

back_log = 600
max_connections=500
max_connect_errors = 6000
wait_timeout=605800

#open_tables = 600
#table_cache = 650
#opened_tables = 630

max_allowed_packet = 32M

sort_buffer_size = 4M
join_buffer_size = 4M
thread_cache_size = 300
query_cache_type = 1
query_cache_size = 256M
query_cache_limit = 2M
query_cache_min_res_unit = 16k

tmp_table_size = 256M
max_heap_table_size = 256M

key_buffer_size = 256M
read_buffer_size = 1M
read_rnd_buffer_size = 16M
bulk_insert_buffer_size = 64M

lower_case_table_names=1

default-storage-engine = INNODB

innodb_buffer_pool_size = 1G
innodb_log_buffer_size = 32M
innodb_log_file_size = 128M
innodb_flush_method = O_DIRECT

#####################
long_query_time= 2
slow-query-log = on
slow-query-log-file = /home/mysql/logs/mysql-slow.log

[mysqldump]
quick
max_allowed_packet = 32M

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

### 初始化数据库

```text
mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/data
```

### 查看数据库服务是否已经启动

```text
# systemctl start mysqld
# systemctl status mysqld
# ps -ef | grep mysql
```

### 设置数据库root用户密码

```text
# mysql_secure_installation
```

这里只说明下MySQL5.7.14版本中，用户密码策略分成低级 LOW 、中等 MEDIUM 和超强 STRONG 三种，推荐使用中等 MEDIUM 级别！

### 登录mysql数据库

```text
# mysql -u root -p
```

## 数据库用户及权限配置

### 新建用户，并赋予权限

```text
mysql> CREATE USER "yy"@"%" IDENTIFIED BY '123';
mysql> grant all on `testdb`.* to "yy"@"%" Identified by "123";
mysql> flush privileges;
```
