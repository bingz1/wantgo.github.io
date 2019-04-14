---
title: Linux命令使用总结
tags: linux
---

1. 搜索文件内容
```
find . -name ".*" | xargs -n 20 grep '/Users/zhaimi/scripts'
```
2. Nginx中查询访问量最高的10个IP
```
cat access_ssl.mobile.o.bike.log | awk '{a[$1]++} END {for(b in a) print b"\t"a[b]}' | sort -k2 -r -n | head -n 50
```