---
title: hive的安装
tags: hadoop
---
1. [MySQL安装推荐](https://www.cnblogs.com/EasonJim/p/7147787.html)
2. [配置hive](https://www.cnblogs.com/jchubby/p/5449350.html)
```
<property>
	<name>hive.exec.local.scratchdir</name>
	<value>/usr/local/hive/iotmp</value>
</property>
<property>
	<name>hive.exec.scratchdir</name>
	<value>/tmp/hive</value>
</property>
<property>
<name>hive.server2.logging.operation.log.location</name>
<value>/usr/local/hive/iotmp</value>
</property>
<property>
<name>hive.downloaded.resources.dir</name>
<value>/usr/local/hive/iotmp</value>
</property>
<property>
<name>hive.querylog.location</name>
<value>/usr/local/hive/iotmp</value>
</property>
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hive?characterEncoding=UTF-8&useSSL=false</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.cj.jdbc.Driver</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>r3[vs5Utmk6Iceg6</value>
  </property>

```

开启外部访问服务
```
 hive --service hiveserver2&
```

3. 加载Nginx日志
```
create external table lblog (
request_time string,
upstream_response_time string,
remote_addr string,
upstream_addr string,
time_local string,
host string,
method string,
request_url string,
request_schema string,
status string,
bytes_sent string,
http_referer string,
http_user_agent string,
gzip_ratio string,
http_x_forwarded_for string,
server_addr string
)
partitioned by (ds string)
row format serde 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe'
with serdeproperties ('input.regex' = '(.*)\\s(.*)\\s(.*)\\s-\\s(.*)\\[(.*)\\]\\s(.*)\\s\"(.*)\\s(.*)\\s(.*)\"\\s(.*)\\s(.*)\\s\"(.*)\"\\s\"(.*)\"\\s\"(.*)\"\\s\"(.*)\"\\s-\\s\"(.*)\"','output.format.string' = '%1$s %2$s %3$s %4$s %5$s %6$s %7$s %8$s %9$s %10$s %11$s %12$s %13$s %14$s %15$s %16$s')
location '/lblog';
```
4. 加载打点日志
```
CREATE TABLE `log_app_ios` (
  `userid` int,
  `eventname` String ,
  `eventdes` String ,
  `eventvalue` String ,
  `eventvalue2` String,
  `eventvalue3` String,
  `eventvalue4` String,
  `eventtime` String,
  `countrycode` String,
  `longitude` String,
  `latitude` String,
  `ip` String,
  `deviceid` String,
  `devicemode` String ,
  `osversion` String ,
  `appversion` String,
  `currency` String ,
  `wifi` tinyint ,
  `time` String 
) 
-- Use RegEx to parse each line
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'
WITH serdeproperties(
  "input.regex" = "([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]* [^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]* [^ ]*)",
  "output.format.string" = "%1$s %2$s %3$s %4$s %5$s %6$s %7$s %8$s %9$s %10$s %11$s %12$s %13$s %14$s %15$s %16$s %17$s %18$s %19$s"
)
-- This also supports gzip, just have to have a .gz extension
STORED AS TEXTFILE
location 'hdfs://10.1.2.38:9000/applog';
```
