---
title: spring-boot-mybaties
tags: spring-boot
---

本地调试  打印SQL语句
mybatis-config.xml文件的 settings 标签下 增加如下配置

```
<setting name="logImpl" value="STDOUT_LOGGING" />
```
