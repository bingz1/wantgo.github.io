---
title: Mac下PostGis的安装
tags: postgre
---

1.安装postgre
```
brew install postgresql -v 
```
2.安装postgis
```
brew install postgis
```
3.初始化本机环境
```
initdb /usr/local/var/postgres -E utf8
```
数据库默认用户是系统当前用户 
```
The files belonging to this database system will be owned by user "****".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

creating directory /usr/local/var/postgres ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /usr/local/var/postgres -l logfile start
```
查看版本
```
psql -V
psql (PostgreSQL) 9.6.4
```
4.启动数据库
```
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
```
创建数据库 默认用户未当前系统用户
```
createdb testDB 
```
连接数据库
```
psql testDB
```
安装gis插件
```
CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
```
查看安装的插件信息
```
testDB=# \dx
                                         List of installed extensions
       Name       | Version |   Schema   |                             Description                             
------------------+---------+------------+---------------------------------------------------------------------
 plpgsql          | 1.0     | pg_catalog | PL/pgSQL procedural language
 postgis          | 2.3.2   | public     | PostGIS geometry, geography, and raster spatial types and functions
 postgis_topology | 2.3.2   | topology   | PostGIS topology spatial types and functions
(3 rows)
```
5.编辑是否需要密码
编辑pg_hba.conf文件
trust 表示不需要密码
MD5 表示需要密码
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
# IPv6 local connections:
host    all             all             ::1/128                 trust

```
6. 更改密码
 alter user zhaimi with password 'testDB';
 将文件设置为需要密码

 