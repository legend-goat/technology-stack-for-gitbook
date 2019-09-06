# 网络配置

## Centos7.x 网络配置

### VirtualBox 网络配置

#### 动态IP配置

虚拟机中设置第一块网卡为\(NAT\)。

```text
# 查看网络配置
~$ ip addr

# 配置网络。增加 ONBOOT=no 修改为 ONBOOT=yes 即可。
~$ vi /etc/sysconfig/network-scripts/ifcfg-enp-0s3
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=f0ca66a7-ddf0-4227-a396-93260ee875f1
DEVICE=enp0s3
ONBOOT=yes

# 重启网络，使配置生效
~$ systemctl restart network.service

# 尝试是否可以上网
~$ ping baidu.com
```

#### 静态IP配置

为了局域网同网段可以访问，我们需要配置一个静态IP。在虚拟机中新增一块网卡为\(Host-Only\)。

```text
# copy 一份配置
~$ cp /etc/sysconfig/network-scripts/ifcfg-enp-0s3 /etc/sysconfig/network-scripts/ifcfg-enp-0s8

# 修改配置。
# 注释掉 BOOTPROTO=dhcp 配置。修改 NAME=enp0s8 ONBOOT=enp0s8。修改UUID和enp0s3保持不同。
# 新增最后两行
~$ vi /etc/sysconfig/network-scripts/ifcfg-enp-0s8
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s8
UUID=f0ca66a7-ddf0-4227-a396-93260ee875f2
DEVICE=enp0s8
ONBOOT=yes
IPADDR=192.168.1.102
NETMASK=255.255.255.0

# 重启网络，使配置生效
~$ systemctl restart network.service
```





