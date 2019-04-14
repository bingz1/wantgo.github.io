---
title: MySQL使用总结
tags: mysql
---
1.添加字段设置默认创建时间
```sql
ALTER TABLE `table_name`
ADD COLUMN  `CreateTime` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;
```
将字段更新为默认创建时间
```sql
ALTER TABLE `table_name`
MODIFY COLUMN  `CreateTime` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;
```
2.添加字段设置默认更新时间
```sql
ALTER TABLE `table_name`
ADD COLUMN `UpdateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ;
```
将字段修改为默认更新时间
```sql
ALTER TABLE `table_name`
MODIFY COLUMN `UpdateTime` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ;
```
3.更新以前数据
  场景:以前没有创建时间字段,这个字段是有两个其他字段代表的,两个字段只有一个字段有值,更新之前的数据的时候,采用下面的方法:
```sql
update `order` set created_at = ifnull(book_time,start_time) ;
```
4.删除索引
```sql
ALTER TABLE table_name DROP INDEX index_name
```