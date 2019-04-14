---
title: aws rds相关参数设置
tags: mysql
---
 记录一下比较常用的设置

1. 启用慢查询
   slow_query_log：要创建慢速查询日志，请设置为 1。默认值为 0
   long_query_time：要防止在慢速查询日志中记录快速运行的查询，请指定需要记录的最短查询执行时间值，以秒为单位。默认值为 10 秒，最小值为 0。如果 log_output = FILE，则可以指定精确到微秒的浮点值。如果 log_output = TABLE，则必须指定精确到秒的整数值。系统只记录执行时间超过 long_query_time 值的查询。例如，将 long_query_time 设置为 0.1 可防止记录任何运行时间少于 100 毫秒的查询。
   log_queries_not_using_indexes：要将所有不使用索引的查询记录到慢速查询日志，请设置为 1。默认值为 0。
2.提交主从备份速度,降低延迟
  innodb_flush_log_at_trx_commit也可以设置为0来提高sql的执行效率
3. 跳过复制过程中的不重要错误
   ```
   CALL mysql.rds_skip_repl_error;
   ```