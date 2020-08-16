### 1、redis操作

```go
brew services start redis 启动并前台运行
brew services stop redis 停止服务
redis-server /usr/local/etc/redis.conf 启动并后台运行
mysql -uroot 本地登录
brew services start mysql 前台
mysql.server start 后台
```

## redis的功能

redis缓存，可用来做分布式锁，支持事务，持久化，lua脚本，lru事件驱动，多集群方案。

## 数据类型

- string
- list
- set
- hash
- zset

## redis过期时间

1. 定期删除：redis默认是每隔 100ms 就**随机抽取**一些设置了过期时间的key，检查其是否过期，如果过期就删除。
2. 惰性删除：查询的时候再删。

## redis内存淘汰机制

1. Volatile-lru
2. volatile-ttl
3. volatile-random
4. allkeys-lru
5. allkeys-random
6. no-eviction

## redis持久化机制

Redis的一种持久化方式叫快照（snapshotting，RDB）,另一种方式是只追加文件（append-only file,AOF）

RDB：从内存写入磁盘

AOF：记录操作的步骤

1. Redis可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。快照持久化是Redis默认采用的持久化方式。

2. 与快照持久化相比，AOF持久化 的实时性更好，因此已成为主流的持久化方案。

Redis 4.0 开始支持 RDB 和 AOF 的混合持久化（默认关闭，可以通过配置项 `aof-use-rdb-preamble` 开启）。

## redis事务

Redis 通过 MULTI、EXEC、WATCH 等命令来实现事务(transaction)功能。

## 缓存雪崩

缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法：

- 事前：尽量保证整个 redis 集群的高可用性，发现机器宕机尽快补上。选择合适的内存淘汰策略。
- 事中：本地ehcache缓存 + hystrix限流&降级，避免MySQL崩掉
- 事后：利用 redis 持久化机制保存的数据尽快恢复缓存

## 缓存穿透

一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。

解决办法： 有很多种方法可以有效地解决缓存穿透问题，最常见的则是采用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被 这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外也有一个更为简单粗暴的方法（我们采用的就是这种），如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。


