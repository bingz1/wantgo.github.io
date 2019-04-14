---
title: spring-boot-redis
tags: spring-boot
---

需要解决2个问题

1.当redis连接失败时,可以去查询数据库
2.当增加或者删除数据库字段导致序列化或者反序列话失败时,删除缓存信息,查询数据库,重新缓存

解决上面两个问题,两个思路

1.继承RedisTemplate,重写execute 方法,这个需要改变项目中RedisTemplate的引用
2.修改源代码,重新打包,只需要修改项目的maven引用即可

一. 从GitHub上拉取源代码,找到修改点

``` java
public <T> T execute(RedisCallback<T> action, boolean exposeConnection, boolean pipeline) {

		Assert.isTrue(initialized, "template not initialized; call afterPropertiesSet() before using it");

		Assert.notNull(action, "Callback object must not be null");

		RedisConnectionFactory factory = getConnectionFactory();

		RedisConnection conn = null;

		try {

			if (enableTransactionSupport) {

				// only bind resources in case of potential transaction synchronization

				conn = RedisConnectionUtils.bindConnection(factory, enableTransactionSupport);

			} else {

				conn = RedisConnectionUtils.getConnection(factory);

			}

			boolean existingConnection = TransactionSynchronizationManager.hasResource(factory);

			RedisConnection connToUse = preProcessConnection(conn, existingConnection);

			boolean pipelineStatus = connToUse.isPipelined();

			if (pipeline && !pipelineStatus) {

				connToUse.openPipeline();

			}

			RedisConnection connToExpose = (exposeConnection ? connToUse : createRedisConnectionProxy(connToUse));

			T result = action.doInRedis(connToExpose);

			// close pipeline

			if (pipeline && !pipelineStatus) {

				connToUse.closePipeline();

			}

			// TODO: any other connection processing?

			return postProcessResult(result, connToUse, existingConnection);

		} finally {

			RedisConnectionUtils.releaseConnection(conn, factory);

		}

	}

```

我们可以看到,当有异常时,只进行的finally处理,我们加上捕获处理,记录异常信息

```
	public <T> T execute(RedisCallback<T> action, boolean exposeConnection, boolean pipeline) {

		RedisConnectionFactory factory = getConnectionFactory();

		RedisConnection conn = null;

		try {

			if (enableTransactionSupport) {

				// only bind resources in case of potential transaction synchronization

				conn = RedisConnectionUtils.bindConnection(factory, enableTransactionSupport);

			} else {

				conn = RedisConnectionUtils.getConnection(factory);

			}

			boolean existingConnection = TransactionSynchronizationManager.hasResource(factory);

			RedisConnection connToUse = preProcessConnection(conn, existingConnection);

			boolean pipelineStatus = connToUse.isPipelined();

			if (pipeline && !pipelineStatus) {

				connToUse.openPipeline();

			}

            RedisConnection connToExpose = (exposeConnection ? connToUse : createRedisConnectionProxy(connToUse));

            T result = action.doInRedis(connToExpose);

			// close pipeline
			if (pipeline && !pipelineStatus) {

				connToUse.closePipeline();

			}

			// TODO: any other connection processing?
			return postProcessResult(result, connToUse, existingConnection);

		} catch (Exception e){

            logger.error("redis操作异常",e);

            //1. 当由序列化产生的问题时,捕获异常,查询新的数据,重置缓存结果

            //2. 如果是拿不到连接的异常时,直接返回null

		    return null;

        }finally {

			RedisConnectionUtils.releaseConnection(conn, factory);

		}

	}

```

二. 场景测试

 1.将host改错,测试获取不到连接异常

 ![](http://image.wantgo.me/blog/2017-7-12-17-14.jpeg)

 捕获异常

```
org.springframework.data.redis.RedisConnectionFailureException: Cannot get Jedis connection; nested exception is redis.clients.jedis.exceptions.JedisConnectionException: Could not get a resource from the pool

```

取到为null的对象,继续去查询数据库

2.定义时,不指定具体的对象值,试下強转时,是否会发生空指针异常

经测试,无异常

3.在序列化和反序列化异常时,返回null数据,业务正常进行﻿