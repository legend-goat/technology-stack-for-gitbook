# mysql 搭建实战

## 基于单机的mysql编译安装

> **环境**： Centos7.x、mysql5.7

### 编译安装

#### 1、准备

```text
~$ yum -y install gcc gcc-c++ ncurses ncurses-devel cmake bison bison-devel

~$ cd /usr/local/src

~$ wget http://www.sourceforge.net/projects/boost/files/boost/1.59.0/boost_1_59_0.tar.gz
~$ tar -xvzf boost_1_59_0.tar.gz

~$ wget http://101.96.10.47/dev.mysql.com/get/Downloads/MySQL-5.7/mysql-boost-5.7.14.tar.gz
~$ tar -zvxf mysql-boost-5.7.14.tar.gz

~$ mv boost_1_59_0 mysql-5.7.14
~$ cd mysql-5.7.14

~$ groupadd -r mysql
~$ useradd -r -g mysql mysql

~$ mkdir -p /home/mysql/data
~$ mkdir -p /home/mysql/logs
~$ mkdir -p /home/mysql/temp
```

#### 2、进入目录，配置

```text
~$ cmake \
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

#### 3、编译&安装

```text
~$ make
~$ make install
~$ make clean
```

#### 4、开机启动

```text
~$ cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
~$ chmod +x /etc/init.d/mysqld 
~$ systemctl enable mysqld
```

#### 5、目录权限

```text
~$ chown -Rf mysql:mysql /usr/local/mysql
~$ chown -Rf mysql:mysql /home/mysql
```

#### 6、添加环境变量

```text
# 末尾添加以下内容
~$ vi /etc/profile
# mysql env
export PATH=$PATH:/usr/local/mysql/bin:/usr/local/mysql/lib

# 生效
~$ source /etc/profile
```

#### 7、配置

```text
~$ vi /etc/my.cnf

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

#### 8、初始化数据库

```text
~$ mysqld --initialize-insecure --user=mysql --basedir=/usr/local/mysql --datadir=/home/mysql/data
```

#### 9、查看数据库服务是否已经启动

```text
~$ systemctl start mysqld
~$ systemctl status mysqld
~$ ps -ef | grep mysql
```

#### 10、设置数据库root用户密码

```text
~$ mysql_secure_installation
```

这里只说明下MySQL5.7.14版本中，用户密码策略分成低级 LOW 、中等 MEDIUM 和超强 STRONG 三种，推荐使用中等 MEDIUM 级别！

#### 11、登录mysql数据库

```text
~$ mysql -u root -p
```

### 用户及权限配置

#### 1、新建用户，并赋予权限

```text
mysql> CREATE USER "yy"@"%" IDENTIFIED BY '123';
mysql> grant all on `testdb`.* to "yy"@"%" Identified by "123";
mysql> flush privileges;
```

### 主从配置

#### master配置

```text
# 创建数据库
~$ mysql –u root –p
mysql> create database repl;

# 在[mysqld]配置段添加如下字段
~$ vi /etc/my.cnf
server-id=1
log-bin=mysql-bin
log-slave-updates=1
binlog-do-db=repl  #需要同步的数据库,如果没有本行表示同步所有的数据库
binlog-ignore-db=mysql  #被忽略的数据

# 在master机上为slave添加一同步帐号 (开放一个账号用于同步)
mysql> grant replication slave on *.* to 'repl'@'%' identified by '123456';
mysql> flush  privileges;

# 重启master的mysqld服务
~$ service mysqld restart

```

#### slave配置

```text
# 使用show master status，查看master的状态，记录下File和Position，
# 如下列结果中的File:mysql-bin.000004;Position:2205，
# 需要注意的是，这两个字段数据不是固定的，所以配置每个slave节点时，都应该查看一次，
# 再根据实际情况配置
~$ mysql –u root –p
mysql> show master status;

# 修改slave机器中mysql配置文件/etc/my.cnf
~$ vi /etc/my.cnf
server-id=2 #
log-bin= mysql-bin
relay-log= mysql-relay-bin
read-only=1
log-slave-updates=1
replicate-do-db=repl #要同步的数据库,不写本行表示同步所有数据库

# 重启master的mysqld服务
~$ service mysqld restart

# 在slave上验证对master连接
~$ mysql -h192.168.10.86 -urepl -p123456

# 设置slave复制 ，这一步中的参数，请根据实际情况填写，
# 其中，MASTER_LOG_FILE与MASTER_LOG_POS的值，即为步骤2中记录的值。
# 或者也可以使用默认配置MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=0,

CHANGE MASTER TO
MASTER_HOST='192.168.10.86',
MASTER_USER='repl',
MASTER_PASSWORD='123456',
MASTER_PORT=3306,
MASTER_LOG_FILE='mysql-bin.000001',
MASTER_LOG_POS=0,
MASTER_CONNECT_RETRY=10;

# 启动slave
~$ start slave

# 运行show slave status\G查看输出结果，主要查看的是如下图所示，
# Slave_IO_Running和Slave_SQL_Running两列是否都为YES
~$ show slave status
```



