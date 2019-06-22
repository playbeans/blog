---
layout: post
title:  "hadoopq全分布式HA安装"
date:   2019-06-22 09:31:01 +0800
categories: linux
tag: hadoop
---

* content
{:toc}

### 搭建清单

local |NN1 |NN2| DN|ZK|ZKFC|JNN
:---:|:---:|:---:|:---:|:---:|:---:|:---:
nodea1|*||||*|*|*
nodea2||*|*|*|*|*
nodea3|||*|*||*
nodea4|||*|*

### 两个nn节点免密登录
```
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
```

### hadoop备份操作
```
 cp -a hadoop/ hadoop-full/
```

#### 编辑hdfs.xml
1.去掉
```
<property>
          <name>dfs.namenode.secondary.http-address</name>
      	  <value>node07:50090</value>
</property>
```
2.新增如下配置
```

<property>
  <name>dfs.nameservices</name>
  <value>mycluster</value>
</property>
<property>
  <name>dfs.ha.namenodes.mycluster</name>
  <value>nn1,nn2</value>
</property>
<property>
  <name>dfs.namenode.rpc-address.mycluster.nn1</name>
  <value>nodea1:8020</value>
</property>
<property>
  <name>dfs.namenode.rpc-address.mycluster.nn2</name>
  <value>nodea2:8020</value>
</property>
<property>
  <name>dfs.namenode.http-address.mycluster.nn1</name>
  <value>nodea1:50070</value>
</property>
<property>
  <name>dfs.namenode.http-address.mycluster.nn2</name>
  <value>nodea2:50070</value>
</property>

<property>
  <name>dfs.namenode.shared.edits.dir</name>
  <value>qjournal://nodea1:8485;nodea2:8485;nodea3:8485/mycluster</value>
</property>

<property>
  <name>dfs.journalnode.edits.dir</name>
  <value>/var/hadoop/ha/jn</value>
</property>


<property>
  <name>dfs.client.failover.proxy.provider.mycluster</name>
  <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
</property>
<property>
  <name>dfs.ha.fencing.methods</name>
  <value>sshfence</value>
</property>
<property>
  <name>dfs.ha.fencing.ssh.private-key-files</name>
  <value>/root/.ssh/id_dsa</value>
</property>

<property>
   <name>dfs.ha.automatic-failover.enabled</name>
   <value>true</value>
 </property>
```



#### 编辑core.xml
1.修改haddop的配置文件如下
```
/var/hadoop/ha
```
2.新增如下配置
```
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://mycluster</value>
</property>

<property>
   <name>ha.zookeeper.quorum</name>
   <value>nodea2:2181,nodea3:2181,nodea4:2181</value>
</property>
```

#### 同步配置文件到其他的服务器
```
scp core-site.xml  hdfs-site.xml  root@nodea2:`pwd`/
scp core-site.xml  hdfs-site.xml  root@nodea3:`pwd`/
scp core-site.xml  hdfs-site.xml  root@nodea4:`pwd`/
```

### 安装zookper集群
#### 先在nodea2机器上搭建一个集群
1.安装zookeeper
```
tar xf zookeeper-3.4.6.tar.gz -C /opt/zookeeper
```
2.使配置文件生效(zoo.cfg)

```
cp -a zoo_sample.cfg zoo.cfg 
```
3.添加配置
```
dataDir=/var/zookeeper/data
dataLogDir=/var/zookeeper/logs

server.1=nodea2:2888:3888

server.2=nodea3:2888:3888

server.3=nodea4:2888:3888

```
4.设置环境变量
```
vi /etc/profile
新增如下行
export ZOOKEEPER_HOME=/opt/zookeeper/zookeeper-3.4.6
export PATH=$PATH:$ZOOKEEPER_HOME/bin

```
5.配置文件分发（pwd是当前目录的意思）
```
scp profile root@nodea3:`pwd`/
scp profile root@nodea4:`pwd`/
scp -r zookeeper/ root@nodea3:`pwd`/
scp -r zookeeper/ root@nodea4:`pwd`/
```
6.设置唯一标识（标识参考第三步）
```

nodea2中执行echo 1 > /var/zookeeper/data/myid
nodea3中执行echo 2 > /var/zookeeper/data/myid
nodea4中执行echo 3 > /var/zookeeper/data/myid
```

### 逐个启动集群
#### 启动journalnode
1.在nodea1,nodea2,nodea3节点上启动jourlname
```
hadoop-daemon.sh start journalnode
```
2.随意找一个nn节点格式化：
```
[root@nodea1 hadoop]# hdfs namenode -format

启动该节点：
[root@nodea1 hadoop]# hadoop-daemon.sh start namenode
```
3.另外一个启动
```
[root@nodea2 hadoop]# hdfs namenode -bootstrapStandby
```
4.hadoop集群zkf格式化
```
[root@nodea1 hadoop]# hdfs zkfc -formatZK

（ha.ActiveStandbyElector: Successfully created /hadoop-ha/mycluster in ZK.）
```
5.在zookeeper 客户端可见：
zkCli.sh

```
[zk: localhost:2181(CONNECTED) 1] ls /
[hadoop-ha, zookeeper]
[zk: localhost:2181(CONNECTED) 2] ls /hadoop-ha
[mycluster]
```
6.启动hdfs集群;
```
start-dfs.sh
```

### 集群搭建之后再次启动
只需要启动start-dfs.sh 即可
```
start-dfs.sh
```