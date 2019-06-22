---
layout: post
title:  "hadoopq全分布式安装"
date:   2019-06-03 09:31:01 +0800
categories: linux
tag: hadoop
---

* content
{:toc}

### 全分布式hadoop环境搭建（非高可用）
有四台机器，关系如下

nodea1 NameNode
nodea2 DataNode
nodea3 DataNode
nodea4 DataNode

#### 设置hadoop（在上一篇得到的虚拟机上做）
0. cp /opt/hadoop-2.6.5 /opt/hadoop/local(伪分布式做个备份)
1. vi core-site.xml(修改hadoop.tmp.dir)防止文件存储地址冲突
2. vi hdfs-site.xml （修改dfs.replication的值为2）
3. vi slaes 删除原本的内容然后添加

nodea2

nodea3

nodea4



#### 虚拟机克隆
```
删除/etc/udev/rules.d/70-persistent-net.rules 文件，关机，做4个克隆分别是nodea2 nodea3 nodea4
```
#### 免密密登录(nodea1)
```
1. scp  id_dsa.pub   nodea2:`pwd`/nodea1.pub（在nodea1上分别把文件发送到nodea2 nodea3 nodea4 上）
2. cat  nodea1.pub  >> authorized_keys(分别在nodea2 nodea3 nodea4上重复操作)

```
#### 虚拟机配置(参考虚拟机安装)
```
1.主机名称设置 nodea2 nodea3 nodea4
2. etc/hosts 域名解析添加

192.168.159.102 nodea2

192.168.159.103 nodea3

192.168.159.104 nodea4
3 防火墙禁用 
```

### 做完如上操作在nodea1节点上尝试启动
```
 1. 格式化hdfs
 hdfs namenode -format 
 2. start-dfs.sh (启动集群)
 3. http://nodea1:50070 
 4. 停止集群
stop-dfs.sh
```


