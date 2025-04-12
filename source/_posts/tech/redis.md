---
title: Redis
date: 2024-05-04 00:00:00
tags: redis
categories: tech
---

---

## 概念

NoSQL 数据库 (非关系型数据库): K-V 数据库 -- Document 数据库 -- 图数据库

Redis (K-V), MongoDB (Doc), ElasticSearch, Neo4j (Graph)

SQL 数据库的查询语句固定

NoSQL 查询语句各种各样

SQL 事务 -- ACID

NoSQL 对于 ACID 只能基本满足

## Redis

Remote Dictionary Server

Key-Value 数据库

单线程, 每个命令具备原子性

支持数据持久化

支持主从集群, 分片集群

多语言客户端

### 数据结构

Key (String) - Value (Object)

Value 的类型有:

String: "xjp"

Hash (Map): {name: "xjp", age: 70}

List: [A -> B -> C]

Set: {A, B, C}

SortedSet: {A: 1, B:2, C:3}

GEO: 地理坐标

BitMap

HyperLog

### 通用命令

```redis
KEYS [pattern]
DEL key_name
EXISTS
EXPIRE 给key设置有效期
TTL 查看有效期
```

### String 命令

```
SET
GET
MSET 批量
MGET 批量
INCR 整数字符串自增1
INCRBY
INCRBYFLOAT
SETNX 添加 string 类型键值对, 如果 key 不存在则不执行
SETEX 添加 string 类型键值对, 并指定有效期
```



### Key 层级结构

用":"隔开, 例如

`mate-pro:session:id`

### Hash

field: value

```
HSET key field value
HGET key field
HMSET key [f1] [v1] [f2] [v2] ...
HMGET 
HGETALL key  取所有键值对
HKEYS 取所有 field
HVALS 取所有值
HINCRBY key field
HSETNX
```

### List

与 LinkedList 类似

插入快

查询慢

```
LPUSH key ele
LPOP key
RPUSH key ele
RPOP key
LRANGE start end
BLPUSH
BLPOP
```

### Set

HashSet

查找快

支持并集交集运算

```
SADD key ele
SREM key ele
SCARD key key 中的元素个数
SISMEMBER key ele ele 是否在 key 对应的 set 中
SMEMBERS key 获取所有元素

SINTER k1 k2 交集
SDIFF k1 k2 差集
SUNION k1 k2 并集
```

### SortedSet (ZSet)

每个元素有一个默认的属性 score

```
ZADD key score member
ZREM key member
ZSCORE key member
ZRANK key member 获取排名
ZCARD
ZCOUNT key min max
ZINCRBY key increment member
ZRANGE
ZRANGEBYSCORE
ZDIFF, ZINTER, ZUNION
```



## Redis Java 客户端

Jedis 简单, 线程不安全

lettuce

Redission 分布式

Spring Data Redis 兼容了 Jedis 和 lettuce

### Jedis

```java
private Jedis jedis;
@BeforeEach
void setup() {
    jedis = new Jedis("192.168.150.101", 6379);
    jedis.auth("password");
    jedis.select(0);  // 选择库
}
```

### SpringDataRedis

```java

```

