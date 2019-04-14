---
title: Flume 发送 nginx 日志发到hdfs
tags: hadoop
---

1.下载 Flume 1.8.0
```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz
tar -zxvf apache-flume-1.8.0-bin.tar.gz 
mv apache-flume-1.8.0-bin flume
cd flume/
mv flume-env.sh.template flume-env.sh

编辑 flume-env.sh  设置JAVA_HOME

```

2.将Nginx日志输出到控制台
```
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 配置Source
a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.deserializer.outputCharset = UTF-8

# 配置需要监控的日志输出目录
a1.sources.r1.command = tail -F /var/log/nginx/access_ssl.mobile.o.bike.log

a1.sinks.k1.type = logger

# 配置Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000  #通道容量
a1.channels.c1.transactionCapacity = 1000  #每次传输多少条

# 将三者连接
a1.sources.r1.channel = c1
a1.sinks.k1.channel = c1
```

bin/flume-ng agent --conf conf --conf-file conf/flume-conf.properties --name a1 -Dflume.root.logger=INFO,console

3.将日志输出到HDFS
```
# 配置Agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 配置Source
a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.deserializer.outputCharset = UTF-8

# 配置需要监控的日志输出目录
a1.sources.r1.command = tail -F /var/log/nginx/access_ssl.mobile.o.bike.log

# 配置Sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.useLocalTimeStamp = true
a1.sinks.k1.hdfs.path = hdfs://10.1.2.38:9000/nginx-log/%y-%m-%d
a1.sinks.k1.hdfs.filePrefix = mobile
a1.sinks.k1.hdfs.fileSuffix = .log
a1.sinks.k1.hdfs.fileType = DataStream
a1.sinks.k1.hdfs.rollInterval = 300  #防止生成过多小文件 设置成每5分钟一个文件
a1.sinks.k1.hdfs.rollSize = 0
a1.sinks.k1.hdfs.rollCount = 0

# 配置Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000  #通道容量
a1.channels.c1.transactionCapacity = 1000  #每次传输多少条

# 将三者连接
a1.sources.r1.channel = c1
a1.sinks.k1.channel = c1
```
启动
```
nohup bin/flume-ng agent --conf conf --conf-file conf/flume-conf.properties --name a1 &
```

4.查看上传文件信息

在 http://10.1.2.38:9870/explorer.html#/ 可以查看上传的文件信息


[解决权限问题](https://stackoverflow.com/questions/11593374/permission-denied-at-hdfs)
[flume依赖Hadoopjar包问题](https://reverse.org.ua/flume-noclassdeffounderror-orgapachehadoopiosequencefilecompressiontype/)