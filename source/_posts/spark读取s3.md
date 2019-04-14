---
title: spark 读取s3
tags: spark
---
由于我们在新加坡和法兰克福有两个s3的中心,所以在使用spark读取s3的时候,遇到了很多坑,记录一下.

1.访问新加坡中心的s3
```
	val conf = new SparkConf().setMaster("local")
    val sc = SparkSession.builder().config(conf).getOrCreate().sparkContext
    sc.hadoopConfiguration.set("fs.s3n.awsAccessKeyId", "*")
    sc.hadoopConfiguration.set("fs.s3n.awsSecretAccessKey", "*")

    val myRDD = sc.textFile("s3n://***")
 ```

2.访问法兰克福中心
```
	val conf = new SparkConf().setMaster("local")
    val sc = SparkSession.builder().config(conf).getOrCreate().sparkContext
    sc.hadoopConfiguration.set("fs.s3a.access.key", "*")
    sc.hadoopConfiguration.set("fs.s3a.secret.key", "*")
    sc.hadoopConfiguration.set("fs.s3a.endpoint", "s3.eu-central-1.amazonaws.com")

    val myRDD = sc.textFile("s3a://**")
```
需要引入的外部包
```
  "org.apache.hadoop" % "hadoop-aws" % "2.8.0",
  "net.java.dev.jets3t" % "jets3t" % "0.9.4"
```
3.由于新加坡的s3和法兰克福的s3使用的不同协议
所以,新加坡的需要使用 s3n
    法兰克福的使用 s3a, 需要指定节点位置
s3a需要使用 jets3t的0.9.4的版本,所以需要单独添加.

4.参考资料
[法兰克福需要使用s3a](https://issues-test.apache.org/jira/browse/HADOOP-13325)
