---
title: docker 入门
tags: docker
---

```
$ docker run --name webserver -d -p 80:80 nginx
```

这条命令会用 nginx 镜像启动一个容器，命名为 webserver，并且映射了 80 端口，这样我们可以用浏览器去访问这个 nginx 服务器。

进入容器内部
```
docker exec -it webserver bash
```
