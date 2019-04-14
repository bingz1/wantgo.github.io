---
title: postgis的使用
tags: postgre
---
1.查看附近单车功能
2.是否在某个区域内功能
3.附近有哪些多边形

基础查询
```sql
#创建表信息
CREATE TABLE lbs(gid serial PRIMARY KEY, point geography(POINT,4326),polygon geography(POLYGON,4326));

#插入数据
INSERT INTO lbs (gid,point,polygon) VALUES (1,ST_GeographyFromText('SRID=4326;POINT(121.456076 31.206964)'), ST_GeographyFromText('SRID=4326;POLYGON((121.450824 31.202472,121.450824 31.2114555,121.461327 31.202472,121.461327 31.211455,121.450824 31.202472))'));

INSERT INTO lbs (gid,point,polygon) VALUES (2,ST_GeographyFromText('SRID=4326;POINT(121.465170 31.216699)'), ST_GeographyFromText('SRID=4326;POLYGON((121.459918 31.212207,121.459918 31.221191,31.221191 121.470421,121.470421 31.221191,121.459918 31.212207))'));

#查询表信息
select * from lbs;

#按照不同形式查询信息
select ST_AsGeoJson(point) from lbs;
select asText(point) from lbs;
```
功能查询
```sql
#1.查看两个点的距离
SELECT ST_Distance('POINT(121.456076 31.206964)'::geography, 'POINT(121.465170 31.216699)':: geography);

 st_distance  
--------------
 1384.1871299

#2.查看附近1000米的点 ST_DWithin
SELECT * FROM lbs WHERE ST_DWithin(point, ST_GeographyFromText('SRID=4326;POINT(121.456076 31.206964)'), 1000);

#3.查看点是否在对边形内 ST_Within
SELECT gid,ST_AsText(point),ST_AsText(polygon),ST_Within(ST_GeomFromEWKT('SRID=4326;POINT(121.461170 31.216699)'), ST_GeomFromEWKT(ST_AsEWKT(polygon))) FROM lbs ;
SELECT gid,ST_AsText(point),ST_AsText(polygon),ST_Within(ST_GeomFromEWKT('SRID=4326;POINT(121.461170 31.216699)'), polygon) FROM lbs ;

#4.判断两个多边形是否相交 ST_Intersects
SELECT gid,ST_AsText(point),ST_AsText(polygon) FROM lbs where ST_Intersects(ST_GeographyFromText('SRID=4326;POLYGON((121.459918 31.212207,121.459918 31.221191,31.221191 121.470421,121.470421 31.221191,121.459918 31.212207))'), polygon);

#5.查询出不同格式信息的方法
select ST_GeographyFromText('SRID=4326;POLYGON((121.459918 31.212207,121.459918 31.221191,31.221191 121.470421,121.470421 31.221191,121.459918 31.212207))');

select ST_GeomFromEWKT('SRID=4326;POLYGON((121.459918 31.212207,121.459918 31.221191,31.221191 121.470421,121.470421 31.2211917,121.459918 31.212207))');

SELECT 'SRID=4;POINT(0 0)'::geometry;

```

[参考连接](http://www.cnblogs.com/wuhenke/archive/2010/08/02/1790747.html)