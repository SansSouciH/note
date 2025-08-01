# Redis

> Redis中文教程网：https://www.redis.net.cn/tutorial/3501.html

```shell
# 进入redis命令行界面
redis-cli
# 设置、获取密码
config set requirepass 123456
config get requirepass
```

## 常用命令

* Key
  * `keys *`
  * `type [key]`
  * `del [key]`
  * `exists [key]`
  * `ttl [key]`
* String
  * `set [key] [value]`
  * `get [key]`
  * `setnx [key] [value]`
* Hash
  * `hset [key] [field] [value]`
  * `hget [key]`
  * `hdel [key]`
* List
  * `lpush [key] [value1]`
  * `rpop [key]`
* Set
  * `sadd [key] [member1]`
* Zset
  * `zadd [key] [score] [member1]`

---

* 缓存更新策略（先删数据库再删缓存）
  * 内存淘汰
  * 过期淘汰
  * 主动更新
* 缓存穿透
  * 设空值
  * 布隆过滤
* 缓存雪崩
  * 随机过期
  * 多级缓存
* 缓存击穿（热点KEY）
  * 互斥锁
  * 逻辑过期

## 实现代码

### 缓存击穿

```java
private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);
public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit timeUnit) {
    RedisData redisData = new RedisData();
    redisData.setData(value);
    redisData.setExpireTime(LocalDateTime.now().plusSeconds(timeUnit.toSeconds(time)));
    String json = JSONUtil.toJsonStr(redisData);
    redisTemplate.opsForValue().set(key, json);
}
/*
	缓存击穿 + 缓存穿透
*/
public <R, ID> R queryWithLogicalExpire(
    String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallBack, Long time, TimeUnit timeUnit) {
    String key = keyPrefix + id;
    R r = null;
    String json = (String) redisTemplate.opsForValue().get(key);
    if (StrUtil.isBlank(json)) {
        //缓存中未查到值，继续数据库查询，缓存穿透则设置真实过期空值
        r = dbFallBack.apply(id);
        if (Objects.isNull(r)) this.set(key, "", CACHE_NULL_TTL, TimeUnit.MINUTES);
        //查询到，设置逻辑过期
        this.setWithLogicalExpire(key, r, time, timeUnit);
        return r;
    }
    RedisData redisData = JSONUtil.toBean(json, RedisData.class);
    r = JSONUtil.toBean((JSONObject) redisData.getData(), type);
    LocalDateTime expireTime = redisData.getExpireTime();
    if (expireTime.isAfter(LocalDateTime.now())) {
        return r;
    }
    String lockKey = LOCK_SHOP_KEY + id;
    boolean isLock = tryLock(lockKey);
    if (isLock) {
        CACHE_REBUILD_EXECUTOR.submit(() -> {
            try {
                R r1 = dbFallBack.apply(id);
                this.setWithLogicalExpire(key, r1, time, timeUnit);
            } catch (Exception e) {
                throw new RuntimeException(e);
            } finally {
                unlock(lockKey);
            }
        });
    }
    return r;
}
```

### 秒杀业务

**注意点**

* 全局ID生成器（分布式ID）：唯一性、高可用、高性能、单调递增、安全性
  * 利用Redis：符号值 + 时间戳 + 标识符
  * 雪花算法
* 超卖问题
  * 乐观锁、悲观锁

* 一人一单
* 全局锁（分布式锁）
  * setnx
  * Redisson

* Redis优化秒杀（内存阻塞队列异步）
* Redis消息队列实现异步秒杀



异步秒杀优惠券下单全流程：

秒杀下单接口：发送请求 -> 获取用户 -> 获取订单id ->执行Lua脚本 -> 判断Lua脚本返回值 -> 返回值正常直接返回订单id

异步执行方法：静态方法加载Lua脚本和线程池 -> 利用@PostConstruct在类加载到容器后执行init方法 -> 从消息队列中读取消息 -> 解析消息队列中的消息 -> 执行下单逻辑

![image-20250602165142773](img\异步秒杀流程.png)
