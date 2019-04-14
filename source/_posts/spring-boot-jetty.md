---
title: spring-boot-jetty
date: 2017-08-01
tags: jetty
---

1.理解jmeter中的聚合报告
```
Label -为请求取样器的名称

Samples - 用同一个请求取样器，发送请求的数量(注意：该值是不断累计的)。比如，10个线程数设置为10，迭代10次，那么每运行一次测试，该值就增加10*10=100

Average - 默认情况下是单个Request的平均响应时间，当使用了Transaction Controller 时，也可以以Transaction为单位显示平均响应时间

Median - 中位数。表示响应时间本不大于该时间值的请求样本数占总数的50%

90% Line - 表示响应时间不大于该时间值的请求样本数占总数的90%

Min - 针对同一请求取样器，请求样本的最小响应时间

Max - 针对同一请求取样器，请求样本的最大响应时间

Error % - 出现错误的请求样本的百分比

Throughput - 吞吐量以“requests/second、requests /minute、requests /hour”来衡量。 时间单位已经被选取为second，所以，显示速率至少是1.0，即每秒1个请求。 当吞吐量被保存到CVS文件时，采用的是requests/second，所以30.0 requests/second 在CVS中被保存为0.5

Kb/sec - 以Kilobytes/seond来衡量的吞吐量
```
2.Tomcat的简单配置
```yml
	server:
	  tomcat:
	    max-threads: 500   # 默认连接是200个  我们调成500
	    compression: on    # 开启压缩
```
