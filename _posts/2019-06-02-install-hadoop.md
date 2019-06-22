---
layout: post
title:  "hadoop伪分布式安装"
date:   2019-06-02 09:31:01 +0800
categories: linux
tag: hadoop
---

* content
{:toc}


###  hadoop伪分布式环境搭建
（单机版分布式环境）（确保虚拟机关闭防火墙，参考上一篇）
#### 下载
从下面地址中下载jdk和hadoop
> 链接：https://pan.baidu.com/s/1CvSuwIZsxP3M6NCWIhVIQw 
提取码：ifmn 
复制这段内容后打开百度网盘手机App，操作更方便哦

#### 安装java
想办法把jdk安装包导入到虚拟机中（xftp或者vm文件共享），然后安装jdk。
```
1. rpm -ivh  jdk-7u67-linux-x64.rpm
2. vi /etc/profile 添加如下内容
JAVA_HOME=/usr/java/jdk1.7.0_67
JRE_HOME=/usr/java/jdk1.7.0_67/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
3. source /etc/profile
4. java -version

```
#### 免秘钥登录
```
ssh-keygen -t dsa(一路回车)
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys

ssh {其他机域名} （直接可以进去说明测试成功）
```

#### 安装hadoop
```
1. tar zxvf hadoop-2.5.0.tar.gz -C /opt/hadoop/
2. 配置hadoop环境变量，vi /etc/profile添加如下内容
export HADOOP_HOME=/opt/hadoop-2.6.5
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

```

#### 配置hadoop
```

1. 修改hadoop的二次java环境变量 /opt/hadoop-2.6.5/etc/hadoop  
hadoop-env.sh mapred-env.sh yarn-env.sh 
（2.6.5版本一定要添加否则找不到及时在/etc/profi中设置java环境变量）
2. vi core-site.xml
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://nodea1:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/var/hadoop/data</value>
    </property>

3. vi /hdfs-site.xml
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>nodea1:50090</value>
    </property>

 4. vi slaves 添加nodea1
 5. 格式化hdfs
 hdfs namenode -format 
 6. 查看格式化结果
 7. ls -al  /var/hadoop/data/dfs/name/current
 8. start-dfs.sh (启动集群)
```

#### 测试
```
1. jps 查看进程是否有（NameNode DataNode SecondayNameNode 如果都有说明启动成功）
2. http://nodea1:50070 查看web界面（访问不到可能是防火墙没有关闭）
3. 上传文件
创建目录：hdfs dfs  -mkdir -p  /user/root	
查看目录:  hdfs dfs -ls   /
上传文件： hdfs dfs -put  hadoop-2.6.5.tar.gz   /user/root
4. 停止集群
stop-dfs.sh
```