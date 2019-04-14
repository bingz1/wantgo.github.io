---
title: hadoop 3.0 集群安装一
tags: hadoop
---
一. 配置基础环境
1.hadoop 3.0 支持多NameNode模式,我们先尝试用单个NameNode模式.
准备三台机器
10.1.2.38  10.1.2.200 10.1.2.163

2.修改三台机器的hostname.
查看机器的hostname
```shell
hostname
```
编辑hostname
```shell
vim /etc/hostname
```
重启机器后生效
IP和hostname对应如下:
10.1.2.38 hadoop-master 
10.1.2.200 hadoop-slave1 
10.1.2.163 hadoop-slave2

3.ubuntu 安装java8

4.设置三台机器可以自访问和互通
[参考免密登录部分](http://www.ityouknow.com/hadoop/2017/07/24/hadoop-cluster-setup.html)

5.下载hadoop 3.0版本并解压到 /opt 目录下
```  
wget http://mirrors.hust.edu.cn/apache/hadoop/common/hadoop-3.0.0/hadoop-3.0.0.tar.gz
tar xf hadoop-3.0.0.tar.gz /opt
```

6.配置环境变量
```
vim /etc/profile

export HADOOP_HOME=/opt/hadoop-3.0.0
export HADOOP_PREFIX=/opt/hadoop-3.0.0
export HADOOP_HDFS_HOME=/opt/hadoop-3.0.0
export HADOOP_CONF_DIR=/opt/hadoop-3.0.0/etc/hadoop

source /etc/profile
```

二. 配置Hadoop参数
1.创建namenode,datenode,temp目录
/opt/hadoop-3.0.0/data/namenode
/opt/hadoop-3.0.0/data/datanode
/opt/hadoop-3.0.0/data/temp

2.配置core-site.xml 文件
```
<property>
    <name>hadoop.tmp.dir</name>
    <value>file:/opt/hadoop-3.0.0/data/temp</value>
    <description>Abase for other temporary directories.</description>
</property>
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop-master:9000</value>
</property>
<property>
    <name>hadoop.proxyuser.ubuntu.groups</name>
    <value>ubuntu</value>
    <description>Allow the superuser oozie to impersonate any members of the group group1 and group2</description>
</property>

<property>
    <name>hadoop.proxyuser.ubuntu.hosts</name>
    <value>10.1.2.38,127.0.0.1,localhost</value>
    <description>The superuser can connect only from host1 and host2 to impersonate a user</description>
</property>
```

3.配置hdfs-site.xml
```
<property>
   <name>dfs.replication</name>
   <value>2</value>
</property>
<property>
   <name>dfs.namenode.name.dir</name>
   <value>/opt/hadoop-3.0.0/data/namenode</value>
</property>
<property>
   <name>dfs.datanode.data.dir</name>
   <value>/opt/hadoop-3.0.0/data/datanode</value>
</property>
```

4.配置yarn-site.xml
```
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>hadoop-master</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
<property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
</property>
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>49152</value>
</property>
<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>49152</value>
</property>
```

5.配置mapred-site.xml

```
<property>
   <name>mapreduce.framework.name</name>
   <value>yarn</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```
6.配置 worker
```
hadoop-master
hadoop-slave1
hadoop-slave2
```
7.将整个hadoop3.0.0文件夹传到另外两个机器的/opt目录下
三,启动集群
1.格式化
```
bin/hadoop namenode -format
```
2.启动 hdfs
```
./sbin/start-dfs.sh 
```
关闭 hdfs
```
./sbin/stop-dfs.sh
```
3.启动 yarn
```
./sbin/start-yarn.sh 
```
关闭yarn
```
./sbin/stop-yarn.sh 
```
4.查看启动状态
master上
```
jps

31283 Jps
27828 DataNode
27685 NameNode
28365 ResourceManager
28030 SecondaryNameNode
28510 NodeManager

```
slave 上
```
7209 DataNode
7353 NodeManager
8557 Jps
```
http://10.1.2.38:8088/cluster/nodes
http://10.1.2.38:9870/dfshealth.html#tab-overview
```
./bin/hadoop dfsadmin -report

Configured Capacity: 1584913920000 (1.44 TB)
Present Capacity: 1506181877760 (1.37 TB)
DFS Remaining: 1506181787648 (1.37 TB)
DFS Used: 90112 (88 KB)
DFS Used%: 0.00%
Replicated Blocks:
  Under replicated blocks: 0
  Blocks with corrupt replicas: 0
  Missing blocks: 0
  Missing blocks (with replication factor 1): 0
  Pending deletion blocks: 0
Erasure Coded Block Groups: 
  Low redundancy block groups: 0
  Block groups with corrupt internal blocks: 0
  Missing block groups: 0
  Pending deletion blocks: 0

-------------------------------------------------
Live datanodes (3):

Name: 10.1.2.163:9866 (hadoop-slave2)
Hostname: hadoop-slave2
Decommission Status : Normal
Configured Capacity: 528304640000 (492.02 GB)
DFS Used: 28672 (28 KB)
Non DFS Used: 4665520128 (4.35 GB)
DFS Remaining: 502063308800 (467.58 GB)
DFS Used%: 0.00%
DFS Remaining%: 95.03%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 1
Last contact: Fri Dec 29 04:56:45 UTC 2017
Last Block Report: Fri Dec 29 01:57:44 UTC 2017


Name: 10.1.2.200:9866 (hadoop-slave1)
Hostname: hadoop-slave1
Decommission Status : Normal
Configured Capacity: 528304640000 (492.02 GB)
DFS Used: 28672 (28 KB)
Non DFS Used: 4665528320 (4.35 GB)
DFS Remaining: 502063300608 (467.58 GB)
DFS Used%: 0.00%
DFS Remaining%: 95.03%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 1
Last contact: Fri Dec 29 04:56:45 UTC 2017
Last Block Report: Fri Dec 29 02:09:33 UTC 2017


Name: 10.1.2.38:9866 (hadoop-master)
Hostname: hadoop-master
Decommission Status : Normal
Configured Capacity: 528304640000 (492.02 GB)
DFS Used: 32768 (32 KB)
Non DFS Used: 4673646592 (4.35 GB)
DFS Remaining: 502055178240 (467.58 GB)
DFS Used%: 0.00%
DFS Remaining%: 95.03%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 1
Last contact: Fri Dec 29 04:56:46 UTC 2017
Last Block Report: Fri Dec 29 04:24:01 UTC 2017
```
四,查看日志信息
1.logs 目录下存储了启动相关信息
可以查看启动状态
如果出现错误,可以在这里分析
五,参考文档
[一](https://hadoop.apache.org/docs/current/index.html)
[二](http://www.ityouknow.com/hadoop/2017/07/24/hadoop-cluster-setup.html)
[三](http://blog.csdn.net/cc1949/article/details/78836141)

hadoop dfsadmin -refreshNodes 

