---
layout: post
title:  "vm install centos"
date:   2019-05-28 09:31:01 +0800
categories: linux
tag: centos
---

* content
{:toc}

#### 安装vmware 14
不说了
#### 安装centos6.5
下载地址1：http://mirrors.aliyun.com/centos/

下载地址2：http://mirrors.163.com/centos/
具体安装方法也不说了
#### 配置静态ip
1.1 vi /etc/sysconfig/network-scripts/ifcfg-eth0 把现有的的key修改成如下（7的默认网卡名称好像变了）,硬件地址和uuid地址注释掉，因为复制虚拟机的时候会自动生成一个新的。
```
ONBOOT=yes
BOOTPROTO=static
#HWADDR=xxxxxxxxxxx
#UUID=xxxxxxxxxxx
```
1.2 在/etc/sysconfig/network-scripts/ifcfg-eth0 添加如下代码,
```
IPADDR="192.168.159.100"
GATEWAY="192.168.159.2"
NETMASK="255.255.255.0"
DNS1=8.8.8.8
BROADCAST="192.168.159.255"
```
> 注意159不是照搬，查看VMnet8的局域网地址，设置正确的数值
> GATEWAY的地址后一位不是1就是2，刚开始写的1，后来ping不通，我又另起一个配置动态ip的虚拟机查看出来的

1.3 重启网络,执行如下命令
```
service network restart
```

#### 禁用iptables
```
关闭iptables
service iptables stop
永久关闭防火墙
chkconfig iptables off
```
#### 禁用selinux
```
vi /etc/selinux/config
将SELINUX=enforcing改为SELINUX=disabled，然后执行reboot
```

#### 删除/etc/udev/rules.d/70-persistent-net.rules 创建快照之前删除一次
```
rm -fr  /etc/udev/rules.d/70-persistent-net.rules
```

>做完上述操作，一个裸的基本镜像就好了，在虚拟中直接进行克隆就可以快速扩展了,如果在这个基本的虚拟机上有改动，每次改动之后都要操作删除。

----
#### 用虚拟机进行克隆
1 设置静态ip（以复制三台机器为例子）
```
/etc/sysconfig/network-scripts/ifcfg-eth0 
把192.168.159.100变成
192.168.159.101，
192.168.159.201，
192.168.159.202，
```
2 设置本机host
```
vi /etc/sysconfig/network
ip为192.168.159.101的机器，设置hostname=nodea1
ip为192.168.159.201的机器，设置hostname=nodeb1
ip为192.168.159.201的机器，设置hostname=nodeb2
```
3 设置域名解析
vi /etc/hosts
```
192.168.159.101 nodea1
192.168.159.201 nodeb1
192.168.159.202 nodeb2
```
4 重新启动
```
reboot
```
5 测试
```
ping nodea1
ping nodeb1
ping nodeb2
```