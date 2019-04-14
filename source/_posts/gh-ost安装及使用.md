---
title: gh-ost安装及使用
tags: mysql
---
1.由于表结构更改会锁表,当表数据量大时,直接增加字段会对业务影响较大,但是互联网应用会频繁更改功能,不得不面对这样的需求.
  许多大的公司也会遇到这样的问题,GitHub开源了他们自己的方案:gh-ost.
  学习下如何使用
  使用限制:主库必须产生Row格式的二进制日志

2.[github地址](https://github.com/github/gh-ost)
3.安装 
[下载地址](https://github.com/github/gh-ost/releases/tag/v1.0.40)
  可以直接下载编译好的文件,也可以下载源码自己编译,自己编译需要注意本机go版本
  (1)确认go版本
   gh-ost is a Go project; it is built with Go 1.8 (though 1.7 should work as well).
   编译的时候,推荐使用1.8,1.7也是可以的,所以先查看本机安装的go版本
   ```
   ~ go version
	 	go version go1.8.3 darwin/amd64
   ```
   (2)自己直接下载,解压得到 `gh-ost` 文件
   (3)查看版本
      ```
     ./gh-ost --version      
	  		1.0.40
      ```
   (4)为方便使用,将执行文件加到当前系统路径下
   
   vim .bash_profile

   ```
   alias gh-ost="~/soft/gh-ost"
   ```
   使配置文件生效
   source ./.bash_profile

4.使用

  1.更新字段名称
  ```
  gh-ost --host=127.0.0.1 --database=test --table=t_test --user="test" --password="test"  --alter="change column iu_renamed name  VARCHAR(100)" --approve-renamed-columns --assume-rbr --initially-drop-ghost-table --initially-drop-old-table -ok-to-drop-table --allow-on-master --chunk-size 10000 --exact-rowcount  --execute
  ```
  2.添加字段
  ```
    gh-ost --host=127.0.0.1 --database=test --table=t_test --user="test" --password="test"  --alter="add column test_id int(11) DEFAULT NULL COMMENT 'test'" --assume-rbr --initially-drop-ghost-table --initially-drop-old-table -ok-to-drop-table --allow-on-master --chunk-size 10000 --exact-rowcount  --execute
  ```
  3.添加索引
  ```
  gh-ost --host=127.0.0.1 --database=test --table=t_test --user="test" --password="test"  --alter="add index index_test_id(test_id)" --assume-rbr --initially-drop-ghost-table --initially-drop-old-table -ok-to-drop-table --allow-on-master --chunk-size 10000 --exact-rowcount  --execute
  ```
  
 5.参考资料
    [为什么要用工具](http://www.cnblogs.com/wangtao_20/p/3504395.html)
    [功能讲解](http://www.infoq.com/cn/articles/github-mysql-gh-ost-online-change-table-definition-tool)
    [各个属性的含义](https://github.com/wing324/helloworld_zh/blob/master/MySQL/gh-ost/GitHub%E5%BC%80%E6%BA%90MySQL%20Online%20DDL%E5%B7%A5%E5%85%B7gh-ost%E5%8F%82%E6%95%B0%E8%A7%A3%E6%9E%90.md)
    [各种例子](https://github.com/github/gh-ost/tree/master/localtests)
