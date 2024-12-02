# Redis

## Redis基础

> Nosql数据库，主要有：string（字符串）、List（列表）、set（集合）、zset（有序集合）、hash（哈希）

### 五大常用类型

#### String（字符串）

> String类型是一个二进制安全的。意味着String可以包含任何数据，比如jpg图片或者序列化的对象，String类型是Redis最基本的数据类型，一个Redis中字符串value最多可以是512M

基本指令：

* set <key> <value>：设置一个String键值对

* append <key> <value>：在key的value后添加指定value

* strlen <key>：获取key中value的长度

* setnx <key>：只有当key不存在时，设置key的值

拓展指令：

* mset <key1> <value1> <key2> <value2> <key3> <value3>...：设置多个键值对
* msetnx <key1> <value1> <key2> <value2>...：设置多个键值对（当设置中有任何一个key在redis中已经存在那么所有键值对全部设置失败）
* getrange <name> 0 3 ：获取name字符串中下标为[0,3]的值
* setrange <name> 3 <value>：在下标为3的地方插入一个值
* setex <key> <过期时间> <value>：设置指定的过期时间（秒）
* getset <key> <value>：设置新值的同时获取旧值

**Redis中String的数据结构：**

1. String的数据结构为简单动态字符串，是可以修改的字符串，内部结构实现上类似于Java的ArrayList（动态扩容数组），采用了预分配冗余空间来减少内存的频繁分配 
2. String的初始预留空间为1M，当String长度小于1M时，每次扩容空间都会成倍增长，当String长度大于1M时，每次扩容空间为1M.注意String最大存储512M

#### List（列表）

> Redis列表是简单的**字符串链表**，按照插入顺序排序。底层是一个**双向循环链表**，通过两端操作性能比较高，通过索引下标的操作中间表节点性能较差

基本指令：

* lpush <key> <element> ...，rpush <key1> <element> ...：从左边or右边插入n个值

* lpop <key>，rpop <key>：从左边or右边取出1个key中的值，值在键在，值光键亡

* rpoplpush <key1> <key2>：从key1的右边取出一个值加入到key2的左边

* lrange <key> 0 1：获得[0,1]下标的元素（[0,-1]标识获取列表中所有元素），0标识最左边第一个值，-1标识最右边第一个值

* lindex <key> <index>：获取key列表中index下标的元素（下标从0开始）
* llen <key>：获取列表的长度

拓展指令：

* linsert <key> after/before  <value1> <value2>：在key列表中的value1前面或后面加入value2
* lrem <key> <count> <element>：从左边删除count个名为元素element值（从左到右）
* lset <key> <index> <value>：在key列表的index下标上换上新的value值

---

**Redis中List的数据结构：**

1. List的数据结构为快速链表QuickList，在列表元素比较少的情况下会使用一块连续的内存存储，这个结构是ziplist（压缩列表）。它将所有的元素紧挨着一起存储，分配的是一块连续的内存，当数据量比较多的时候才会改成quicklist

#### Set（集合）

> 与list基本一样，不同的是set有自动去重功能，底层是字典的Hash表的结构，做增删查的操作基本都是O(1)的时间复杂度

基本指令：

* sadd <key> <value1> <value2> ...：创建一个key，连续加入多个values
* smembers <key>：取出该集合的所有值
* sismember <key> <value>：判断集合key是否含有该value值，有1，没有0
* scard <key>：返回该集合的元素个数
* srem <key> <value1> <value2> ...：删除key中的多个指定元素
* spop <key>：随机从该集合中吐出一个值
* srandmember <key> <n>：随机从该集合中取出n个值，不会从集合中删除
* smove <source> <destination> value：把集合中一个值从一个集合移动到另一个集合中
* sinter <key1> <key2>：返回两个集合的交集元素
* sunion <key1> <key2>：返回两个集合的并集元素
* sdiff <key1> <key2>：返回两个集合的差集元素

---

**Redis中Set的数据结构：**

1. 和Java中HashSet的实现方式类似（使用HashMap实现），Redis中的Set结构内部同样使用了hash结构，所有的value都指向同一个内部值

#### Hash（哈希）

> Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。类似Java中的Map<String,Object>

基本指令：

* hset <key> <field> <value>：给key集合中的field字段赋上value值
* hget <key> <field>：取出key集合中field中取出value
* hmset <key> <field1> <value1> <field2> <value2>....：批量设置hash的值
* hexists <key> <field>：查看hash表中field字段是否存在
* hkeys <key>：查看key集合中的所有field
* hvals <key>：查看key集合中所有value
* hincrby <key> <field> <number>：为哈希表key中的域field的值加上number，或者-number
* hsetnx <key> <field> <value>：和hset类似，不过只有当field不存在时才能设置成功

---

**Redis中Hash的数据结构：**

1. Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，使用ziplist，否则使用Hashtable

#### Zset（有序集合）

> zset与set很类似，不过zset除了拥有set的特性之外，还能自动排序。有序集合中每个成员都关联了一个评分（score），这个评分被用来按照从最低分到最高分的方式排序集合中的成员。集合的成员是唯一的，但是评分是可以重复的

基本指令：

* zadd <key> <score> <value1> <score> <value2> <score> <value3>...：将一个或者多个member元素及其score值加入到有序集合key当中
* zrange <key> <start> <stop> [withscores]：返回有序集key中，下标在[start,stop]之间的元素，带上withscores可以让分数和值一起返回得到结果集
* zrangebyscore <key> min max [withscores] [limit offset count]：取出评分（score）在[min,max]之间的值。从小到大排序
* zrevrangebyscore  <key> max min [withscores] [limit offset count]：和上方法一样，不过采用从大到小排序

拓展指令：

* zincrby <key> <number> <value>：
* zount <key> min max：
* zrank <key> <value>：返回key中value的排名（0是第一位）

---

Redis中zset的数据结构：

1. hash结构，hash的作用是关联元素value和score，保障元素value的唯一性，可以通过元素value找到响应的score值
2. 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表

---

**拓展：**

incr <key>：将key中存储的数字加1

decr <key>：将key中存储的数字减1

increby <key> 10：将key中存储的数字加10

decrby <key> 10：将key中存储的数字减10

`注意：在redis中incre和decr的操作是原子性的，也就是在多线程中是不会被打断的`

### 常用指令

**key相关**命令：

* keys *：查看当前库中所有的key
* exists key：查看该key是否存在
* type key：查看当前key的类型
* del key：删除该key
* unlink key：删除该key（选择非阻塞删除（异步操作））
* expire key 10：标识给key设置10秒后过期
* ttl key：查看key是否过期（-1标识永不过期，-2标识已经过期）

对**库相关**命令

* dbsize：查看当前redis库中有多少个key
* select 15：切换到15号redis库
* flushdb：清空当前库中所有内容
* flushall：清空所有库中的所有内容
--
拓展：
* 设置密码：config set requirepass 123456
* 获取密码：config get requirepass

---

拓展：

```shell
# 设置
config set requirepass [密码]
# 获取redis密码
config get requirepass
```

## Redis事务、锁、秒杀

#### 事务

> Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按照顺序的执行。事务在执行的过程中，不会被其他客户端发来的命令所打断。Redis事务的主要作用就是**串联多个命令**防止别的命令插队

相关命令：

1. Multi：表示开启事务，进入命令组队阶段，命令按照队列的形式组队
2. Exec：按照组队好的命令顺序执行所有命令
3. discard：组队的过程中通过discard来放弃组队并退出事务

`事务的错误处理：在组队过程中如果出现错误，那么所有组队将会在执行时失败并退出事务，如果组队中未出现错误但在exec后执行出现错误，那么只有那一个出现错误的指令会执行失败，其他未出现错误的指令则会执行成功`

##### 事务冲突

> 使用锁的方式解决事务冲突

* 悲观锁：每次在拿数据之前都会上锁，知道操作完成后才会释放锁，别的线程才可以拿到数据。传统的关系型数据库中用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在操作之前就上锁

* 乐观锁：每次在拿数据时都不会上锁，但是会将每次的操作添加一个版本号机制来判断在同一时刻别的线程有没有去更新这个数据
  * 相关命令：
    * watch <key> -> 监视一个key的所有操作，如果在执行事务之前有别的客户端对该key做了操作那么事务执行失败
    * unwatch <key> -> 取消对key的监视

---

**Redis事务三特性：**

1. 单独的隔离操作
   * 事务中的所有操作命令都会序列化、按照顺序执行，事务执行的过程中不会被其他客户端的命令打断
2. 没有隔离级别的概念
   * 队列中的命令没有提交之前都不会实际被执行，因为事务提交之前的任何指令都不会被执行
3. 不保证原子性
   * 事务中如果有一条指令执行失败，其他的命令依然会顺序执行，没有回滚操作

## Redis持久化

### RDB（redis database）

> 在**指定的事件间隔内**将数据集**快照**写入硬盘（比如每10秒将数据集写入硬盘）**写时复制技术**。

存储过程：

1. 单独创建一个Fork进程并创建一个临时文件
2. 将文件先写入临时文件中（保证数据的一致性和安全性，如果是直接传输到dump.rdb文件中那么在传输过程中如果服务挂掉了那么持久化的数据和redis中的数据就不一致了）
3. 持久化过程结束后将临时文件内容替换上次持久化好的文件（临时文件替换dump.rdb文件中，该文件在redis启动目录中创建）

RDB**特点**：

1. 整个过程不进行任何IO操作，因此速度极快
2. 最后一次持久化后的数据可能丢失（因为rdb是设置每多少秒内如果有多少个key改变了才进行持久化存储，那么如果在）

### AOF（Append Only File）

> * 以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来（读操作不记录），只能追加文件不能改写文件
> * 简单来说就是记录下所有我们的写入指令保存在日志文件当中，当我们重启redis或需要恢复数据时，redis会将日志中的所有指令从前到后执行一次完成数据恢复工作

AOF存储开启方法

```shell
# redis.conf
# 开启AOF存储
appendonly yes
# AOF日志文件名（存储路径默认和RDB存储目录地址一致）
appendfilename "appendonly.aof"
```

AOF文件修复指令

```shell
redis-check-aof --fix appendonly.aof
```

存储过程：

1. 客户端的所有写命令被append追加到AOF缓冲区内
2. AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync（同步）写到AOF文件中
3. AOF文件大小超过配置文件指定范围（默认为2倍）时，会对AOF文件rewrite重写，压缩AOF文件容量

AOF**特点**：

1. 备份机制更稳健，丢失数据概率更低
2. 可读的AOF日志文件，通过操作AOF文件，可处理误触操作
3. 比RDB更耗费磁盘空间
4. 磁盘备份速度比RDB慢
5. 每次读写都同步会有性能压力
6. 如果AOF日志中存在Bug，会造成存储失败

---

**拓展：**

* 当AOF和RDB存储都开启时，redis听取AOF存储方式

## Redis配置文件

### 常用配置

#### 网络、日志配置

```shell
###################################  NETWORK ###################################
# 指定 redis 只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求
bind 127.0.0.1
 
# 是否开启保护模式，默认开启。要是配置里没有指定bind和密码。开启该参数后，redis只会本地进行访问，拒绝外部访问。要是开启了密码和bind，可以开启。否则最好关闭，设置为no
protected-mode yes
 
# redis监听的端口号
port 6379

# 此参数为设置客户端空闲超过timeout，服务端会断开连接，为0则服务端不会主动断开连接，不能小于0
timeout 0

# 是否在后台执行，yes：后台运行；no：不是后台运行
daemonize yes

#redis的进程文件
pidfile /var/run/redis/redis.pid

# 指定了服务端日志的级别。级别包括：debug（很多信息，方便开发、测试），verbose（许多有用的信息，但是没有debug级别信息多），notice（适当的日志级别，适合生产环境），warn（只有非常重要的信息）
loglevel notice

# 指定了记录日志的文件。空字符串的话，日志会打印到标准输出设备。后台运行的redis标准输出是/dev/null
logfile /usr/local/redis/var/redis.log

# 是否打开记录syslog功能
# syslog-enabled no
 
# syslog的标识符。
# syslog-ident redis
 
# 日志的来源、设备
# syslog-facility local0
 
# 数据库的数量，默认使用的数据库是0。可以通过”SELECT 【数据库序号】“命令选择一个数据库，序号从0开始
databases 16
```

#### 快照、持久化配置

##### RDB存储

```shell
###################################  SNAPSHOTTING  ###################################
 
# RDB核心规则配置 save <指定时间间隔> <执行指定次数更新操作>，满足条件就将内存中的数据同步到硬盘中。若不想用RDB方案，可以把 save "" 的注释打开，下面三个注释
# 官方出厂配置默认是 900秒内有1个更改，300秒内有10个更改以及60秒内有10000个更改，则将内存中的数据快照写入磁盘。
# save ""
save 900 1
save 300 10
save 60 10000

# 当RDB持久化出现错误后，是否依然进行继续进行工作，yes：不能进行工作，no：可以继续进行工作，可以通过info中的rdb_last_bgsave_status了解RDB持久化是否有错误
stop-writes-on-bgsave-error yes
 
# 配置存储至本地数据库时是否压缩数据，默认为yes。Redis采用LZF压缩方式，但占用了一点CPU的时间。若关闭该选项，但会导致数据库文件变的巨大。建议开启。
rdbcompression yes

# 是否校验rdb文件;从rdb格式的第五个版本开始，在rdb文件的末尾会带上CRC64的校验和。这跟有利于文件的容错性，但是在保存rdb文件的时候，会有大概10%的性能损耗，所以如果你追求高性能，可以关闭该配置
rdbchecksum yes
 
# 指定本地数据库文件名，一般采用默认的 dump.rdb
dbfilename dump.rdb
 
# 数据目录，数据库的写入会在这个目录。rdb、aof文件也会写在这个目录
dir /usr/local/redis/var
# 在我的虚拟机中存储目录是 dir /mydata/redis/data
```

##### AOF存储

```shell
###################################  APPEND ONLY MODE  ###################################
# Redis 默认不开启AOF存储。它的出现是为了弥补RDB的不足（数据的不一致性），所以它采用日志的形式来记录每个写操作，并追加到文件中。Redis 重启的会根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作默认redis使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是redis如果中途宕机，会导致可能有几分钟的数据丢失，根据save来策略进行持久化，Append Only File是另一种持久化方式，可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入 appendonly.aof 文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。若开启aof则将no改为yes
appendonly no

# 指定本地数据库文件名，默认值为 appendonly.aof
appendfilename "appendonly.aof"
 
# aof持久化策略的配置
# no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快
# always表示每次写入都执行fsync，以保证数据同步到磁盘
# everysec表示每秒执行一次fsync，可能会导致丢失这1s数据
# appendfsync always
appendfsync everysec
# appendfsync no
 
# 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。Linux的默认fsync策略是30秒。可能丢失30秒数据
no-appendfsync-on-rewrite no
 
# aof自动重写配置。当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100，表示大于了100%大小，也就两倍）时，自动启动新的日志重写过程。重写压缩
auto-aof-rewrite-percentage 100
 
# 设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写
auto-aof-rewrite-min-size 64mb

# aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项（redis宕机或者异常终止不会造成尾部不完整现象。）出现这种现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以
aof-load-truncated yes
 
# 加载redis时，可以识别AOF文件以“redis”开头。
# 字符串并加载带前缀的RDB文件，然后继续加载AOF尾巴
aof-use-rdb-preamble yes
```



## 原理部分

### 数据结构

#### 动态字符串SDS

> SDS（Simple Dynamic String）。Redis中key是字符串、value是字符串集合。基本上redis中的所有结构都可以看作字符串

`传统字符串时，无法直接获得字符串长度、非二进制安全（不能包括特殊字符）、不可修改。`

`动态字符串中SDS是一个结构体。`

* 分为两个部分：头部分 + 数据部分
  * 头部份主要存储：1.字符串长度  2.申请的内存空间大小  3.字符串类型（8位最小、64位最大）
  * 数据部分是主要存储的数据。

特点：

1. 获取字符串长度的时间复杂度为O(1)
2. 支持动态扩容 + 预分配内存：当字符串小于1M时字符串每次双倍+1扩容。当字符串大于1M时字符串每次+1M+1扩容（后面的+1就是预分配）。
3. 二进制安全的。'\0并不会像c语言中一样成为字符串的结束符号，因此在Redis的String类型中可以存储任意类型。'

`因为Linux用户直接操作硬件，只能调用内核函数来操作硬件分配出内存。因此每次的内存分配都会占用非常多的资源。因此我们要尽量减少内存分配。也叫减少用户态和内核态之间的转换。`

#### ZipList

> 在Redis3.2之前会单独用到ZipList。是一种特殊的双端链表，而且是一段**连续的内存空间**。结构内不存在指针（减少了指针带来的内存消耗）。但是ZipList也可以实现双端插入数据。而且做增删操作时时间复杂度为O(1)。

重要的四个参数：

1. 总字节数zlbytes
2. 尾偏移量zltail
3. 结束标识符：0xff
4. entry节点的个数zllen

每个entry节点中的重要参数：

1. 前一个节点的长度previous_entry_length
2. 编码属性encoding
3. content：负责保存节点的数据 

特点：

1. 压缩列表可以看作为一个"双向链表"。在头信息中包括zipList总大小和尾偏移指针，因此可以快速访问到尾部
2. 列表之间不是通过指针连接，而是记录上一个节点长度和本节点的长度来寻址。
3. 如果列表数过多，导致链表过长可能会影响查询性能
4. 在做修改时，可能会造成连续级联更新问题

#### QuickList

> 在ZipList中是在内存中申请一片连续的内存空间，但是内存空间很少有较大的连续内存空间，因此引申出了QuickList。每个节点都关联着一个ZipList。

`双端链表。每个节点关联了一个ZipList。多个占用较小内存空间的ZipList申请连续内存空间，然后被QuickList用指针关联起来。并且QuickList可以限制每个ZipList的内存大小。(list-max-ziplist-size)`

![image-20221028233113678](设置ZipList链表所占的内存大小.png)

`QuickList还可以对ZipList做压缩。默认不压缩首尾的ZipList`

特点：

1. 是每个节点都是ZipList的双端链表
2. 每个节点都是ZipList，解决了传统链表中指针占用内存过多的问题。（ZipList是一块连续内存）
3. 控制ZipList的大小，解决了连续内存申请效率问题
4. 中间节点可以压缩，进一步节省内存

#### IntSet

> Redis中Set集合的一种实现方式，名整数集合。基于数组实现，具备可变长度、有序等特征。

重要三个参数：

1. 编码方式（分为一个content16字节、32字节、64字节这几种）
2. 元素个数
3. 数据实体

特点：

1. 如果遇到了比编码格式大的数则会进行整个IntSet的编码升级。编码升级首先是倒序从最后将最后一个元素放到指定位置，依次向前（不倒序的话前面边长的元素可能会覆盖后面元素的空间）
2. IntSet会保证元素唯一、有序
3. 具备动态扩容，针对小数据量和大数据量有对应的编码方式，节省内存空间
4. 因为是数组且保证了有序所以底层采用二分查找来查询，查询效率比O(n)高

#### Dict

> Hash的底层结构。底层主要是由：哈希表、哈希节点、字典来组成的

重要参数：

1. table：指向entry的指针
2. size：哈希表的大小
3. sizemask：哈希表大小的掩码
4. used：entry节点的个数

每个entry中的重要参数：

1. key：键
2. next：下一个entry的指针（出现哈希冲突的时候才会使用到next指向下一个节点）
3. union：val。节点中存储数据的变量

### 网络模型

### 通信协议

### 内存策略

## 面试相关

### 1. Redis缓存相关问题

#### 1.1 缓存穿透

**缓存穿透**是指查询一个数据库一定不存在的数据。

我们以前正常的使用Redis缓存的流程大致是：

1、数据查询首先进行缓存查询

2、如果数据存在则直接返回缓存数据

3、如果数据不存在，就对数据库进行查询，并把查询到的数据放进缓存

4、如果数据库查询数据为空，则不放进缓存



例如我们的数据表中主键是自增产生的，所有的主键值都大于0。此时如果用户传入的参数为-1，会是怎么样？这个-1，就是一定不存在的对象。程序就会每次都去查询数据库，而每次查询都是空，每次又都不会进行缓存。假如有人恶意攻击，就可以利用这个漏洞，对数据库造成压力，甚至压垮我们的数据库。



为了防止有人利用这个漏洞恶意攻击我们的数据库，我们可以采取如下措施：

如果从数据库查询的对象为空，也放入缓存，key为用户提交过来的主键值，value为null，只是设定的缓存过期时间较短，比如设置为60秒。这样下次用户再根据这个key查询redis缓存就可以查询到值了（当然值为null），从而保护我们的数据库免遭攻击。

#### 1.2 缓存雪崩

缓存雪崩，是指在某一个时间段，缓存集中过期失效。在缓存集中失效的这个时间段对数据的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。

为了避免缓存雪崩的发生，我们可以将缓存的数据设置不同的失效时间，这样就可以避免缓存数据在某个时间段集中失效。例如对于热门的数据（访问频率高的数据）可以缓存的时间长一些，对于冷门的数据可以缓存的时间段一些。甚至对于一些特别热门的数据可以设置永不过期。

#### 1.3 缓存击穿

缓存击穿，是指一个key非常热点（例如双十一期间进行抢购的商品数据），在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求到数据库上，就像在一个屏障上凿开了一个洞。

我们同样可以将这些热点数据设置永不过期就可以解决缓存击穿的问题了。

### 2. Redis集群方案

单机Redis的读写速度非常快，能够支持大量用户的访问。虽然Redis的性能很高，但是对于大型网站来说，每秒需要获取的数据远远超过单台redis服务所能承受的压力，所以我们迫切需要一种方案能够解决单台Redis服务性能不足的问题。这就需要使用到Redis的集群了。Redis集群有多种方案，下面分别进行讲解。

#### 2.1 主从复制Replication

redis支持主从复制的模式。

在主从复制模式下Redis节点分为两种角色：主节点(也称为master)和从节点(也称为slave)。这种模式集群是由一个主节点和多个从节点构成。

原则：Master会将数据同步到slave，而slave不会将数据同步到master。Slave启动时会连接master来同步数据。

![1](1.png)



这是一个典型的分布式读写分离模型。我们可以利用master来处理写操作，slave提供读操作。这样可以有效减少单个机器的并发访问数量。

要实现主从复制这种模式非常简单，主节点不用做任何修改，直接启动服务即可。从节点需要修改redis.conf配置文件，加入配置：slaveof <主节点ip地址> <主节点端口号>，例如master的ip地址为192.168.200.129，端口号为6379，那么slave只需要在redis.conf文件中配置slaveof 192.168.200.129 6379即可。



分别连接主节点和从节点，测试发现主节点的写操作，从节点立刻就能看到相同的数据。但是在从节点进行写操作，提示  `READONLY You can't write against a read only slave`  不能写数据到从节点。

现在我们就可以通过这种方式配置多个从节点进行读操作，主节点进行写操作，实现读写分离。

#### 2.2 哨兵sentinel

我们现在已经给Redis实现了主从复制，可将主节点数据同步给从节点，实现了读写分离，提高Redis的性能。但是现在还存在一个问题，就是在主从复制这种模式下只有一个主节点，一旦主节点宕机，就无法再进行写操作了。也就是说主从复制这种模式没有实现高可用。那么什么是高可用呢？如何实现高可用呢？

#### 2.2.1 高可用介绍

高可用(HA)是分布式系统架构设计中必须考虑的因素之一，它是通过架构设计减少系统不能提供服务的时间。保证高可用通常遵循下面几点：

1. 单点是系统高可用的大敌，应该尽量在系统设计的过程中避免单点。
2. 通过架构设计而保证系统高可用的，其核心准则是：冗余。
3. 实现自动故障转移。

#### 2.2.2 Redis sentinel介绍

sentinel(哨兵)是用于监控redis集群中Master状态的工具，其本身也是一个独立运行的进程，是Redis 的高可用解决方案，sentinel哨兵模式已经被集成在redis2.4之后的版本中。

sentinel可以监视一个或者多个redis master服务，以及这些master服务的所有从服务；当某个master服务下线时，自动将该master下的某个从服务升级为master服务替代已下线的master服务继续处理请求，并且其余从节点开始从新的主节点复制数据。

在redis安装完成后，会有一个redis-sentinel的文件，这就是启动sentinel的脚本文件，同时还有一个sentinel.conf文件，这个是sentinel的配置文件。

sentinel工作模式：

![2](2.png)

注意：可能有些同学会有疑问，现在我们已经基于sentinel实现了高可用，但是如果sentinel挂了怎么办呢？其实sentinel本身也可以实现集群，也就是说sentinel也是高可用的。

##### 2.2.3 Redis sentinel使用

###### 2.2.3.1 配置sentinel

Sentinel在redis的安装包中有，我们直接使用就可以了，但是先需要修改配置文件，执行命令：

```bash
cd /usr/local/redis/

# 复制sentinel配置文件
cp /root/redis-4.0.14/sentinel.conf sentinel01.conf

# 修改配置文件：
vi sentinel01.conf
```

在sentinel01.conf配置文件中添加：

```bash
# 外部可以访问
bind 0.0.0.0
sentinel monitor mymaster 127.0.0.1 6379 1
sentinel down-after-milliseconds mymaster 10000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1

```

`注意：如果有sentinel monitor mymaster 127.0.0.1 6379 2配置，则注释掉。`



参数说明：

- sentinel monitor mymaster 192.168.200.129 6379 1

  `mymaster` 主节点名,可以任意起名，但必须和后面的配置保持一致。

  `192.168.200.128 6379` 主节点连接地址。

  `1`  将主服务器判断为失效需要投票，这里设置至少需要 1个 Sentinel 同意。

- sentinel down-after-milliseconds mymaster 10000

  设置Sentinel认为服务器已经断线所需的毫秒数。

- sentinel failover-timeout mymaster 60000

  设置failover（故障转移）的过期时间。当failover开始后，在此时间内仍然没有触发任何failover操作，当前
  sentinel 会认为此次failover失败。

- sentinel parallel-syncs mymaster 1

  设置在执行故障转移时， 最多可以有多少个从服务器同时对新的主服务器进行同步， 这个数字越小，表示同时进行同步的从服务器越少，那么完成故障转移所需的时间就越长。

###### 2.2.3.2 启动sentinel

配置文件修改后，执行以下命令，启动sentinel：

```bash
/root/redis-4.0.14/src/redis-sentinel sentinel01.conf
```

效果如下：

![3](3.png)

可以看到，6379是主服务，6380和6381是从服务。

###### 2.2.3.3 测试sentinel

我们在6379执行shutdown，关闭主服务，Sentinel提示如下：

```bash
+sdown master mymaster 192.168.200.129 6379	#主节点宕机
+odown master mymaster 192.168.200.129 6379 #quorum 1/1 
+new-epoch 1
+try-failover master mymaster 192.168.200.129 6379 #尝试故障转移
+vote-for-leader 00a6933e0cfa2b1bf0c3aab0d6b7a1a6455832ec 1 #选举领导
+elected-leader master mymaster 192.168.200.129 6379
+failover-state-select-slave master mymaster 192.168.200.129 6379 #故障转移选择从服务
+selected-slave slave 192.168.200.129:6380 192.168.200.129 6380 @ mymaster 192.168.200.129 6379
#故障转移状态发送 发送到6380
+failover-state-send-slaveof-noone slave 192.168.200.129:6380 192.168.200.129 6380 @ mymaster 192.168.200.129 6379
+failover-state-wait-promotion slave 192.168.200.129:6380 192.168.200.129 6380 @ mymaster 192.168.200.129 6379
+promoted-slave slave 192.168.200.129:6380 192.168.200.129 6380 @ mymaster 192.168.200.129 6379
+failover-state-reconf-slaves master mymaster 192.168.200.129 6379
+slave-reconf-sent slave 192.168.200.129:6381 192.168.200.129 6381 @ mymaster 192.168.200.129 6379
+slave-reconf-inprog slave 192.168.200.129:6381 192.168.200.129 6381 @ mymaster 192.168.200.129 6379
+slave-reconf-done slave 192.168.200.129:6381 192.168.200.129 6381 @ mymaster 192.168.200.129 6379
+failover-end master mymaster 192.168.200.129 6379 #故障转移结束，原来的主服务是6379
+switch-master mymaster 192.168.200.129 6379 192.168.200.129 6380 #转换主服务，由原来的6379转为现在的6380
+slave slave 192.168.200.129:6381 192.168.200.129 6381 @ mymaster 192.168.200.129 6380
+slave slave 192.168.200.129:6379 192.168.200.129 6379 @ mymaster 192.168.200.129 6380
+sdown slave 192.168.200.129:6379 192.168.200.129 6379 @ mymaster 192.168.200.129 6380
```

根据提示信息，我们可以看到，6379故障转移到了6380，通过投票选择6380为新的主服务器。



在6380执行info

```bash
# Replication
role:master
connected_slaves:1
slave0:ip=127.0.0.1,port=6381,state=online,offset=80531,lag=1
```



在6381执行info

```bash
# Replication
role:slave
master_host:127.0.0.1
master_port:6380
master_link_status:up
```



故障转移如下图：

![4](4.png)

#### 2.3 Redis内置集群cluster

##### 2.3.1 Redis cluster介绍

Redis Cluster是Redis的内置集群，在Redis3.0推出的实现方案。在Redis3.0之前是没有这个内置集群的。Redis Cluster是无中心节点的集群架构，依靠Gossip协议协同自动化修复集群的状态。

Redis cluster在设计的时候，就考虑到了去中心化，去中间件，也就是说，集群中的每个节点都是平等的关系，都是对等的，每个节点都保存各自的数据和整个集群的状态。每个节点都和其他所有节点连接，而且这些连接保持活跃，这样就保证了我们只需要连接集群中的任意一个节点，就可以获取到其他节点的数据。

Redis cluster集群架构图如下：

![5](5.png)

##### 2.3.2 哈希槽方式分配数据

需要注意的是，这种集群模式下集群中每个节点保存的数据并不是所有的数据，而只是一部分数据。那么数据是如何合理的分配到不同的节点上的呢？

Redis 集群是采用一种叫做`哈希槽 (hash slot)`的方式来分配数据的。redis cluster 默认分配了 16384 个slot，当我们set一个key 时，会用`CRC16`算法来取模得到所属的`slot`，然后将这个key 分到哈希槽区间的节点上，具体算法就是：`CRC16(key) % 16384`。

假设现在有3个节点已经组成了集群，分别是：A, B, C 三个节点，它们可以是一台机器上的三个端口，也可以是三台不同的服务器。那么，采用`哈希槽 (hash slot)`的方式来分配16384个slot 的话，它们三个节点分别承担的slot 区间是：

> - 节点A覆盖0－5460
> - 节点B覆盖5461－10922
> - 节点C覆盖10923－16383

那么，现在要设置一个key ,比如叫`my_name`:

```
set my_name itcast
```

按照redis cluster的哈希槽算法：`CRC16('my_name')%16384 = 2412`。 那么就会把这个key 的存储分配到 节点A 上了。

##### 2.3.3 Redis cluster的主从模式

redis cluster 为了保证数据的高可用性，加入了主从模式，一个主节点对应一个或多个从节点，主节点提供数据存取，从节点则是从主节点拉取数据备份，当这个主节点挂掉后，就会在这些从节点中选取一个来充当主节点，从而保证集群不会挂掉。

redis cluster加入了主从模式后的效果如下：

![6](6.png)

##### 2.3.4 Redis cluster搭建

###### 2.3.4.1 准备Redis节点

为了保证可以进行投票，需要至少3个主节点。

每个主节点都需要至少一个从节点,所以需要至少3个从节点。

一共需要6台redis服务器，我们这里使用6个redis实例，端口号为7001~7006。



先准备一个干净的redis环境，复制原来的bin文件夹，清理后作为第一个redis节点，具体命令如下：

```bash
# 进入redis安装目录
cd /usr/local/redis
# 复制redis
mkdir cluster
cp -R bin/ cluster/node1
# 删除持久化文件
cd cluster/node1
rm -rf dump.rdb
rm -rf appendonly.aof
# 删除原来的配置文件
rm -rf redis.conf
# 复制新的配置文件
cp /root/redis-4.0.14/redis.conf ./
# 修改配置文件
vi redis.conf
```

集群环境redis节点的配置文件如下：

```bash
# 不能设置密码，否则集群启动时会连接不上
# Redis服务器可以跨网络访问
bind 0.0.0.0
# 修改端口号
port 7001
# Redis后台启动
daemonize yes
# 开启aof持久化
appendonly yes
# 开启集群
cluster-enabled yes
# 集群的配置 配置文件首次启动自动生成
cluster-config-file nodes.conf
# 请求超时
cluster-node-timeout 5000
```

第一个redis节点node1准备好之后，再分别复制5份，

```bash
cp -R node1/ node2
```

修改六个节点的端口号为7001~7006，修改redis.conf配置文件即可

编写启动节点的脚本：

```bash
vi start-all.sh
```

内容为：

```bash
cd node1
./redis-server redis.conf
cd ..
cd node2
./redis-server redis.conf
cd ..
cd node3
./redis-server redis.conf
cd ..
cd node4
./redis-server redis.conf
cd ..
cd node5
./redis-server redis.conf
cd ..
cd node6
./redis-server redis.conf
cd ..
```

设置脚本的权限，并启动：

```bash
chmod 744 start-all.sh
./start-all.sh
```

使用命令  ps -ef | grep redis 查看效果如下：

![7](7.png)

###### 2.3.4.2 启动Redis集群

redis集群的管理工具使用的是ruby脚本语言，安装集群需要ruby环境，先安装ruby环境：

```bash
# 安装ruby
yum -y install ruby ruby-devel rubygems rpm-build

# 升级ruby版本，redis4.0.14集群环境需要2.2.2以上的ruby版本
yum install centos-release-scl-rh
yum install rh-ruby23  -y
scl enable rh-ruby23 bash

# 查看ruby版本
ruby -v
```



下载符合环境要求的gem，下载地址如下：

https://rubygems.org/gems/redis/versions/4.1.0

课程资料中已经提供了redis-4.1.0.gem，直接上传安装即可，安装命令：

```bash
gem install redis-4.1.0.gem
```



进入redis安装目录，使用redis自带的集群管理脚本，执行命令：

```bash
# 进入redis安装包
cd /root/redis-4.0.14/src/
# 查看集群管理脚本
ll *.rb
# 使用集群管理脚本启动集群，下面命令中的1表示为每个主节点创建1个从节点
./redis-trib.rb create --replicas 1 127.0.0.1:7001 127.0.0.1:7002 \
127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006
```

效果如下：

```bash
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
192.168.200.129:7001
192.168.200.129:7002
192.168.200.129:7003
Adding replica 192.168.200.129:7005 to 192.168.200.129:7001
Adding replica 192.168.200.129:7006 to 192.168.200.129:7002
Adding replica 192.168.200.129:7004 to 192.168.200.129:7003
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: f0094f14b59c023acd38098336e2adcd3d434497 192.168.200.129:7001
   slots:0-5460 (5461 slots) master
M: 0eba44418d7e88f4d819f89f90da2e6e0be9c680 192.168.200.129:7002
   slots:5461-10922 (5462 slots) master
M: ac16c5545d9b099348085ad8b3253145912ee985 192.168.200.129:7003
   slots:10923-16383 (5461 slots) master
S: edc7a799e1cfd75e4d80767958930d86516ffc9b 192.168.200.129:7004
   replicates ac16c5545d9b099348085ad8b3253145912ee985
S: cbd415973b3e85d6f3ad967441f6bcb5b7da506a 192.168.200.129:7005
   replicates f0094f14b59c023acd38098336e2adcd3d434497
S: 40fdde45b16e1ac85c8a4c84db75b43978d1e4d2 192.168.200.129:7006
   replicates 0eba44418d7e88f4d819f89f90da2e6e0be9c680
Can I set the above configuration? (type 'yes' to accept): yes #注意选择为yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join..
>>> Performing Cluster Check (using node 192.168.200.129:7001)
M: f0094f14b59c023acd38098336e2adcd3d434497 192.168.200.129:7001
   slots:0-5460 (5461 slots) master
   1 additional replica(s)
M: ac16c5545d9b099348085ad8b3253145912ee985 192.168.200.129:7003
   slots:10923-16383 (5461 slots) master
   1 additional replica(s)
S: cbd415973b3e85d6f3ad967441f6bcb5b7da506a 192.168.200.129:7005
   slots: (0 slots) slave
   replicates f0094f14b59c023acd38098336e2adcd3d434497
S: 40fdde45b16e1ac85c8a4c84db75b43978d1e4d2 192.168.200.129:7006
   slots: (0 slots) slave
   replicates 0eba44418d7e88f4d819f89f90da2e6e0be9c680
M: 0eba44418d7e88f4d819f89f90da2e6e0be9c680 192.168.200.129:7002
   slots:5461-10922 (5462 slots) master
   1 additional replica(s)
S: edc7a799e1cfd75e4d80767958930d86516ffc9b 192.168.200.129:7004
   slots: (0 slots) slave
   replicates ac16c5545d9b099348085ad8b3253145912ee985
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

##### 2.3.5 使用Redis集群

按照redis cluster的特点，它是去中心化的，每个节点都是对等的，所以连接哪个节点都可以获取和设置数据。

使用redis的客户端连接redis集群，命令如下：

```bash
./redis-cli -h 192.168.200.129 -p 7001 -c
```

其中-c 一定要加，这个是redis集群连接时，进行节点跳转的参数。

连接到集群后可以设置一些值，可以看到这些值根据前面提到的哈希槽方式分散存储在不同的节点上了。

## Redis客户端

> Jedis、Lettuce、Redisson

* Jedis：学习成本低，方法名就是Redis命令。Jedis实例线程不安全，多线程环境下需要连接池使用。
* Lettuce：基于Netty实现，支持同步、异步和响应式编程，线程安全。支持Redis哨兵、集群、管道模式。
* Redisson：基于Redis实现的分布式、可伸缩的Java数据结构集合。包含：Map、Queue、Lock、AtomicLong、Semaphore

### Jedis

1. 引入依赖

   ```xml
   <dependency>
   	<groupId>redis.clients</groupId>
       <artifactId>jedis</artifactId>
   	<version>3.7.0</version>
   </dependency>
   ```

2. 建立连接

   ```java
   private Jedis jedis;
   
   //单元测试注解
   @BeforeEach
   void setUp() {
       //建立连接
       jedis = new Jedis("127.0.0.1", 6379);
       //设置密码
       jedis.auth("xxxxxxxxx");
       //选择库0 ~ 15
       jedis.select(0);
   }
   
   @Test
   void test() {
       //方法名称就是redis命令的名称。常用五大数据类型名称：set、lpush、sadd、hset、zset
       jedis.set("key", "value");
       String value jedis.get("key");
   }
   
   @AfterEach
   void shutDown() {
       //释放资源
       if (jedis != null) {
           jedis.close();  //close方法在用到连接池时会自动归还连接对象
       }
   }
   ```

3. 配置连接池

   ```java
   //Jedis直连大量创建销毁连接性能消耗大，且线程不安全
   //配置Jedis连接池
   public class JedisConnectionFactory{
       private static final JedisPool jedisPool;
       
       static {
           //连接池配置
           JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
           jedisPoolConfig.setMaxTotal(10);  //最大连接数
           jedisPoolConfig.setMaxIdle(10);  //最大空闲连接
           jedisPoolConfig.setMinIdle(0);  //最小空闲连接
           jedisPoolConfig.setMaxWaitMillis(200);  //最长等待时间
           
           jedisPoolConfig = new JedisPool(jedisPoolConfig, "127.0.0.1", 6379, 1000, "auth");  //（配置对象、Ip、端口、timeout、auth）
       }
       
       static Jedis getJedis() {
           return jedisPool.getResource();
       }
   }
   ```

### Spring Data Redis

1. 引入依赖

   ```xml
   <!--redis依赖-->
   <dependency>
   	<groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   <!--连接池依赖.Lettuce和Jedis都是依赖commons-pool2连接池-->
   <dependency>
   	<groupId>org.apache.commons</groupId>
       <artifactId>commons-pool2</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   spring:
     redis:
       host: 127.0.0.1
       port: 6379
       password: 123456789
       lettuce:
         pool:
           max-active: 10  #最大连接数
           max-idle: 10  #最大空闲连接
           min-idle: 0	  #最小空闲连接
           max-wait: 100  #连接等待时间
   ```

> SpringData中包括对 JDBC、JPA、Redis、Elasticsearch、Hadoop的封装。

* Spring Data Redis整合了Lettuce和Jedis，并且提供了RedisTemplate统一API操作Redis
* 支持Redis发布订阅、哨兵、集群、Lettuce响应式编程、JDK、JSON，字符串，Spring对象的序列化和反序列化、基于Redis的JKDCollection实现

| API                             | 返回值类型      | 说明               |
| ------------------------------- | --------------- | ------------------ |
| redisTemplate.**opsForValue()** | ValueOperations | 操作String数据类型 |
| redisTemplate.**opsForList()**  | ListOperations  | 操作List数据类型   |
| redisTemplate.**opsForHash()**  | HashOperations  | 操作Hash数据类型   |
| redisTemplate.**opsForSet()**   | SetOperations   | 操作Set数据类型    |
| redisTemplate.**opsForZSet()**  | ZSetOperations  | 操作ZSet数据类型   |

`存储时redis默认使用jdk序列化器存储对象（ObjectOutPutStream）：可读性较差、内存占用较大`

* **自动**序列化。修改ReidsTemplate中使用的序列化器：默认使用jdk序列化器会将字节分解，造成存储数据可读性差，内存占用大

  ```java
  @Configuration
  public class RedisConfig {
  
      @Bean
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
          RedisTemplate<String, Object> template = new RedisTemplate<>();
          template.setConnectionFactory(redisConnectionFactory);
  
          template.setKeySerializer(RedisSerializer.string());
          template.setHashKeySerializer(RedisSerializer.string());
  
          template.setValueSerializer(RedisSerializer.json());
          template.setHashValueSerializer(RedisSerializer.json());
  
          return template;
      }
  }
  ```

* **手动**序列化。使用StringRedisTemplate可以直接存储、取出字符串。使用对象json化、取出对象object化

  ```java
  @Resource
  private StringRedisTemplate stringRedsTemplate = new StringRedisTemplate();
  
  @Test
  void test() {
      User user = new User();
      user.setName("zhangsan");
      user.setAge(22);
      
      String userJson = JSONUtil.toJsonStr(user);  //hutool转json工具
      
      stringRedsTemplate.opsForValue().set("user:0", userJson);
      String json =  stringRedsTemplate.opsForValue().get("user:0");
      
      User userRes = JSONUtil.toBean(json, User.class);
  }
  ```

# 常用业务

## 缓存业务

> 单服务缓存业务逻辑

```java
//缓存和数据库中保证：1.保证缓存和数据库原子性  2.保证线程安全（多线程并发问题）
//缓存策略1：先操作数据库，再操作缓存。
//缓存策略2：只在缓存中做crud，创建一个异步线程来检测缓存中的变化，定时将缓存写入数据库。
//缓存策略3：缓存和数据库整合为统一服务，对外暴露API接口，无需调用者关心一致性。
```

* 缓存同步逻辑（查询）

  ```java
  //1.设置缓存key
  String key = CACHE_KEY + id;
  //2.从redis中查询缓存
  String shopJson = stringRedisTemplate.opsForValue().get(key);
  //3.判断缓存是否存在，存在则直接返回
  if (StrUtil.isNotBlank(shopJson)) {
      Shop shop = JSONUtil.toBean(shopJson, Shop.class);
      return Result.ok(shop);
  }
  //4.根据id查询数据库
  Shop data = getById(id);
  if (data == null) {
      return Result.fail("该数据不存在");
  }
  //5.将数据库中的数据写入缓存中。设置超时时间30min
  stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), CACHE_DATA_TTL, TimeUnit.MINUTES);
  return Result.ok();
  ```

* 缓存同步逻辑（修改）

  ```java
  //先操作数据库，再操作缓存。每次操作数据库后主动删除缓存。
  Long id = data.getId();
  if (id == null) {
      return Result.fail("数据不存在");
  }
  //1.操作数据库
  updateById(id);
  //2.删除缓存
  stringRedisTemplate.delete(CACHE_DATA_KEY + id);  //如果是用分布式系统，则是使用MQ发送一则通知其他微服务
  return Result.ok();
  ```

### 缓存穿透

> 外部查询缓存中完全不存在的key，造成查询绕过缓存直接访问数据库。

> 解决办法：
>
> 1. 将数据库查询不到的值作为null加入到缓存中，查询id为缓存的key。
> 2. 设计布隆过滤器，在查询缓存之前进行布隆过滤一遍。
>
> 增加缓存查询**id存储的复杂程度**，避免出现规律性，添加对缓存的**数据校验**，对用户的访问进行**权限校验**，做好**热点参数的限流**。

`第一种解决缓存穿透的方法：生成无效的key，造成内存浪费。但便于管理，在设置null值时设置ttl可以解决内存浪费。但可能造成缓存数据库不一致。`

`第二种解决缓存穿透的方法：布隆过滤实现复杂。统计算法无法完全保证过滤正确。`

### 缓存雪崩

> 指大量缓存的key同时失去有效期或者Redis服务宕机。

> 解决方法：
>
> 1. 给不同的key添加不同的TTL值，避免在导入数据时的过期时间大量重复
> 2. 利用Redis集群**提高服务的可用性**
> 3. 给**缓存业务添加降级限流策略**（请求快速失败，避免请求堆积访问数据库）
> 4. 给业务添加**多级缓存**（nginx静态缓存 -> jvm本地缓存 -> redis多级缓存 -> 数据库）

### 缓存击穿

> 热点key问题。部分key被高并发访问并且缓存重建业务比较复杂的key突然失效了，无数的请求访问会在瞬间给数据库带来巨大冲击。

> 解决方法：
>
> 1. 互斥锁：若线程A查询缓存未命中，则A立马进入重建缓存，并且在重建缓存业务中拿到互斥锁，在缓存数据恢复之后再释放锁。在其他线程缓存未命中时同样去获取互斥锁，获取不到互斥锁则表示有线程正在进行缓存重建，则等待一段时间再重试获取缓存数据。
>
>    ![image-20230106204351106](互斥锁解决缓存击穿.png)
>
> 2. 逻辑过期：线程A，B，C同时向服务发起查询。A查询数据发现数据逻辑过期（当前时间已超过最初设定过期时间）时A开启互斥锁，并单独开放一个线程进行缓存数据重构，A则继续查询拿到旧的过期数据返回给调用者。B和A同时进行查询，B也发现数据发生逻辑过期，B也想开启互斥锁重构数据但是此时互斥锁在A线程上，则B直接绕开重构缓存数据返回旧的数据。在A开启的重构数据线程完成重构后释放互斥锁，C在释放锁之后直接访问则查询到的是缓存中的值。

`互斥锁：1.没有额外的内存消耗 2.保证了缓存和数据库强一致性 3.实现简单，只需要在原有业务上加锁`

`逻辑过期：1.线程无需等待，性能较好 2.不保证一致性 3.有额外的内存消耗、实现复杂`

* 保证数据库和缓存的强一致性则选择互斥锁解决缓存穿透
* 不保证强一致性，追求性能极致则选择逻辑过期

> 避免缓存穿透+缓存击穿常见代码逻辑：
>
> 1. 调用接口，查询缓存
> 2. 缓存命中直接返回 
> 3. A调用者未命中拿到互斥锁进入数据库**重构**缓存逻辑 
> 4. B调用者未命中且未拿到互斥锁进入**读取**数据库数据逻辑 
> 5. A查询数据库中有值完成缓存重构，A查询数据库中没有值将null加入缓存。（无论如何A在拿到互斥锁后都会给这个key添加一个缓存值）
> 6. B递归后重新从redis中拿到缓存值返回调用者

#### 互斥锁

```java
//使用setnx实现redis简单互斥锁（在设置setnx时一定要设置一个ttl过期时间避免宕机无法删除setnx数据造成数据阻塞）
//当高并发使用setnx时只有一个请求能设置成功。
public Result queryWithPassThrough(Long id) {
    String key = CACHE_SHOP_KEY + id;
    //1.查询缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    if (StrUtil.isNotBlank(shopJson)) {
        //2.缓存中有数据直接返回
        Shop shop = JSONUtil.toBean(shopJson, Shop.class);
        return Result.ok(shop);
    } else {
        try {
            //3.缓存未命中，加互斥锁重构缓存
            String keyLock = "lock:cache:" + key;
            boolean isLock = tryLock(keyLock);  //是否成功获得互斥锁
            if (!isLock) {
                //获取互斥锁失败
                Thread.sleep(50);
                queryWithPassThrough(id);  //重试
            }
            //4.查询数据库
            Shop shop = baseMapper.selectById(id);
            if (shop == null) {
                //5.数据库中也没有数据，将空值写入redis
                stringRedisTemplate.opsForValue().set(key, "", CACHE_SHOP_TTL, TimeUnit.MINUTES);
                return Result.fail("数据不存在");  //返回错误信息
            }
            //6.数据库中存在数据，写入redis
            stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), CACHE_SHOP_TTL, TimeUnit.MINUTES);
            return Result.ok();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            //7.释放互斥锁
            unlock(key);
        }
    }
}
```

#### 逻辑过期

```java
//非侵入的新建一个redisData类，避免设置逻辑过期时间需要修改源代码
@Data
public class RedisData {
    private LocalDateTime expireTime;
    private Object data;
}

@Resource
private StringRedisTemplate stringRedisTemplate = new StringRedisTemplate();

private static final CACHE_SHOP_KEY = "cache:key:shop";
private static final ExecutorService executors = Executors.newFixedThreadPool(1);

//缓存穿透 + 缓存击穿（逻辑过期）两种问题解决业务逻辑
public Result queryWithLogicalExpire(Long id) {
    String key = CACHE_SHOP_KEY + id;
    //1.查询缓存
    String shopJson = stringRedisTemplate.opsForValue().get(key);
    if (StrUtil.isNotBlank(shopJson)) {
        //2.命中，判断是否逻辑过期
        RedisData redisData = JSONUtil.toBean(shopJson, RedisData.class);
        Shop shop = (Shop) redisData.getData();
        LocalDateTime expireTime = redisData.getExpireTime();
        if (expireTime.isAfter(LocalDateTime.now())) {
            //2.1未过期，直接返回
            return Result.ok(shop);
        }
        //2.2过期，获取互斥锁
        String keyLock = "lock:shop:" + key;
        boolean isLock = tryLock(keyLock);
        if (isLock) {
            //3.成功拿到锁，开新线程做缓存重构
            executors.submit(()->{
                try {
                    //3.1查询数据信息
                    Shop data = this.getById(id);  //获取一个需要设置逻辑过期时间的对象
                    //2.封装逻辑过期数据
                    RedisData redisData1 = new RedisData();
                    redisData1.setData(data);
                    redisData1.setExpireTime(LocalDateTime.now().plusSeconds(20L));  //从当前时间之后的expireSeconds秒
                    //3.写入Redis
                    stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData1));
                } catch (Exception e) {
                    throw new RuntimeException();
                } finally {
                    unlock(keyLock);
                }
            });
        }
        //4.返回旧缓存数据
        return Result.ok(shop);
    } else {
        //未命中，查询数据库。
        Shop shop = getById(id);
        if (shop == null) {
            //数据库中也没有数据，将空值写入redis
            stringRedisTemplate.opsForValue().set(key, "", CACHE_SHOP_TTL, TimeUnit.MINUTES);
            return Result.fail("数据不存在");  //返回错误信息
        }
        //数据库中存在数据，写入redis
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(shop), CACHE_SHOP_TTL, TimeUnit.MINUTES);
        return Result.ok(shop);
    }
}
```

### 缓存通用工具类

> 解决缓存穿透+缓存击穿的工具类，需要提前引入hutool工具类。（有瑕疵）

```java
package com.hmdp.utils;

import cn.hutool.core.util.BooleanUtil;
import cn.hutool.core.util.StrUtil;
import cn.hutool.json.JSONObject;
import cn.hutool.json.JSONUtil;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

import java.time.LocalDateTime;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.function.Function;

@Component
public class CacheClient {
    private final StringRedisTemplate stringRedisTemplate;

    private static final ExecutorService executors = Executors.newFixedThreadPool(1);

    public CacheClient(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }


    public void set(String key, Object value, Long time, TimeUnit unit) {
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(value), time, unit);
    }

    public void setWithLogicalExpire(String key, Object value, Long time, TimeUnit unit) {
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
    }

    public <R, ID> R queryWithLogicalExpireAndPassThrough(
            String keyPrefix, ID id, Class<R> type, Function<ID, R> dbFallback, Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        String json = stringRedisTemplate.opsForValue().get(key);
        if (StrUtil.isNotBlank(json)) {
            //缓存命中
            RedisData data = JSONUtil.toBean(json, RedisData.class);
            LocalDateTime expireTime = data.getExpireTime();
            R r = JSONUtil.toBean((JSONObject) data.getData(), type);
            if (expireTime.isAfter(LocalDateTime.now())) {
                //未过期，直接返回
                return r;
            }
            //过期，尝试拿互斥锁开启新线程做缓存重构
            String keyLock = "lock:data:" + key;
            boolean isLock = tryLock(keyLock);
            if (isLock) {
                //拿到互斥锁
                executors.submit(() -> {
                    try {
                        //开启新线程做缓存重构
                        RedisData redisData = new RedisData();
                        R r1 = dbFallback.apply(id);
                        redisData.setData(r1);
                        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
                        stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
                    } catch (Exception e) {
                        throw new RuntimeException();
                    } finally {
                        //重构完毕释放互斥锁
                        unlock(keyLock);
                    }
                });
            }
            //直接返回
            return r;
        } else {
            //TODO 这里高并发会有多次访问数据库的情况
            R r = dbFallback.apply(id);
            if (r == null) {
                stringRedisTemplate.opsForValue().set(key, "", time, unit);
                return null;
            }
            RedisData redisData = new RedisData();
            redisData.setData(r);
            redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(time)));
            stringRedisTemplate.opsForValue().set(key, JSONUtil.toJsonStr(redisData));
            return r;
        }
    }

    private boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 30L, TimeUnit.SECONDS);
        return BooleanUtil.isTrue(flag);
    }

    private void unlock(String key) {
        stringRedisTemplate.delete(key);
    }

    static class RedisData {
        private LocalDateTime expireTime;
        private Object data;

        public LocalDateTime getExpireTime() {
            return expireTime;
        }

        public void setExpireTime(LocalDateTime expireTime) {
            this.expireTime = expireTime;
        }

        public Object getData() {
            return data;
        }

        public void setData(Object data) {
            this.data = data;
        }
    }
}
```

## 秒杀业务

> 全局唯一ID、优惠券秒杀下单流程、超卖问题、一人一单、分布式锁、Redis优化秒杀、消息队列异步秒杀

### 全局唯一ID

> 分布式唯一ID微服务。1.唯一性  2.高可用  3.高性能  4.整体递增  5.安全性（ID不能太有规律）

![image-20230109225324120](全局唯一ID.png)

**ID组成部分**：由64个比特位来组成

1. 符号位：永远为0，表示永远为正数
2. 时间戳：31bit，以秒为单位
3. 序列号：32bit，秒内的计数器，支持每秒钟2^32个不同的ID

`除Redis外snowflake算法或数据库自增都可以实现分布式全局唯一ID的实现`

```java
//Redis实现分布式全局唯一ID实例
@Component
public class RedisIdWorker {

    private final StringRedisTemplate stringRedisTemplate;

    public RedisIdWorker(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    /**
     * 开始时间戳
     */
    private static final long BEGIN_TIMESTAMP = 1640995200L;

    /**
     * 序列号位数
     */
    private static final int BIT_COUNT = 32;

    public long nextId(String keyPrefix) {
        //1.生成时间戳
        LocalDateTime now = LocalDateTime.now();
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);
        long timeStamp = nowSecond - BEGIN_TIMESTAMP;
        //2.生成序列号
        //2.1获取当前日期作为前缀，精确到天
        String date = now.format(DateTimeFormatter.ofPattern("yyyyMMdd"));
        long count = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" +date);
        //3.拼接并返回
        return timeStamp << BIT_COUNT | count;
    }
}
```

### 秒杀下单

> 特价的优惠券往往需要抢购。在维护普通券表时维护一个特价优惠券，关联普通优惠券，节省内存资源。

`单独维护特价优惠券表：优惠券基本信息、优惠金额、使用规则、优惠券库存、开始抢购时间，结束抢购时间。`

注意：

* 秒杀是否开始或结束，过期后无法下单
* 库存是否充足，不足则无法下单

**超卖问题**

1. 使用悲观锁：sychronized、lock、数据库锁。
   1. 简单粗暴、性能一般

2. 使用乐观锁：设立version字段，每次修改数据库后version+1，如果在第一次查询出的version和查询之前的version值不同则不对数据库做修改。
   1. 性能较好、存在成功率低的问题
   2. 当遇到只能通过CAS相等来判断是否消费时可以采用：将库存分表存储查询来提高访问成功率。


```java
//解决超卖问题实例
public void fun(Long id){
    //1.查出优惠券信息
    Voucher voucher = Mapper.selectById(id);
    //2.判断优惠时间是否开始
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        //现在时间在开始时间之前
        return Result.error("优惠尚未开始");
    }
    //3.判断优惠时间是否结束
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        //现在时间在结束时间之后
        return Result.error("优惠已经结束");
    }
    //4.判断优惠券是否充足
    if (voucher.getStock() < 1) {
        return Result.error("优惠券不足");
    }
    //5.扣减库存
    boolean flag = voucharMapper.update()
        						.setsql("stock = stock - 1")
        						.eq("id", voucher.getId())
        						.gt("stock", 0);  //在对数据库操作时添加一个CAS的操作，如果在修改库存时库存的数量和之前查询出来的数量不同则表示该值在修改之前有其他线程已经完成了修改。（解决超卖问题时则不需要stock=voucher.getStock()，只需要在修改时判断库存是否大于0即可）
    if (!flag) {
        return Result.error("优惠券扣减失败");
    }
    //...写入创建订单的业务逻辑
    return Result.ok();
}
```

### 一人一单

> 同一张优惠券每个用户只能领取一次

![abc](abc.png)

```java
//一人一单实例：在前面秒杀下单流程实例中添加一个判断条件（根据优惠券的id和用户id来查询订单，查出数据则代表已经下过单，没有则表示可以下单）
public Result updateVoucher(Long id) {
    Voucher voucher = baseMapper.selectById(id);
    if (LocalDateTime.now().isBefore(voucher.getBeginTime())) {
        return null;
    }
    if (LocalDateTime.now().isAfter(voucher.getEndTime())) {
        return null;
    }
    if (voucher.getStock() < 1) {
        return null;
    }
    //1.check操作：在update前判断一次该用户是否下过单（线程不安全）
    //2.在check方法上添加互斥锁
    //3.优化互斥锁：不锁整个方法。只判断是否是同一用户：对每个用户的id加锁
    //4.对用户id加锁时需要注意获取用户id的真实值，并且使用intern将真实id加入到常量池中以免漏判（toString方法会new一个新的地址空间）
    //5.如果使用Transactional关键字注意：spring的事务代理是在方法结束后进行事务提交，因此需要在方法外使用synchronized关键字
    //6.在其他方法调用Transactional注解修饰的方法时考虑spring代理事务失效问题：
    	//1.范围修饰符 2.事务传播范围错误 3.未使用代理对象调用事务方法 4.多线程导致数据库连接不一致
    Long userId = UserHolder.getUser().getId();
    synchronized (userId.toString().intern()) {
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();  //拿到代理对象调用事务方法则执行事务，否则使用this中非事务方法
        return proxy.createVoucherOrder(voucher.getId());
    }
}
```

`使用synchronized互斥锁时无法在分布式系统中保持线程安全。（解决方法：使用setnx实现互斥效果）`

* 使用SETNX命令解决分布式系统中的线程安全问题（分布式锁）

```shell
# 添加锁，NX是互斥，EX是设置超时时间：避免在设置setnx后在设置expire之前服务宕机导致锁无法释放的问题
SET lock [名称] EX 10 NX
```

* 核心思想就是在redis中记录每个用户的操作，根据用户id作为key存储在senx中保证每个用户只能并发访问接口一次

```java
public interface ILock {
    boolean tryLock(long timeoutSecond);  //尝试加分布式锁
    void unlock();  //解锁
}

import org.springframework.data.redis.core.StringRedisTemplate;
import java.util.concurrent.TimeUnit;

public class SimpleRedisLockDemo implements ILock{
    private String name;
    private StringRedisTemplate stringRedisTemplate;
    private static final String KEY_PREFIX = "lock:";
    
    public SimpleRedisLockDemo(String name, StringRedisTemplate stringRedisTemplate) {
        this.name = name;
        this.stringRedisTemplate = stringRedisTemplate;
    }
    //非阻塞加分布式锁
    @Override
    public boolean tryLock(long timeoutSecond) {
        long threadId = Thread.currentThread().getId();
        Boolean success = stringRedisTemplate.opsForValue()
                .setIfAbsent(KEY_PREFIX + name, threadId + "", timeoutSecond, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);  //避免包装类拆箱时因线程安全返回null
    }
    //释放锁
    @Override
    public void unlock() {
        stringRedisTemplate.delete(KEY_PREFIX + name);
    }
}
```

### 分布式锁误删

> redis设置分布式锁时常设置expire时间，若线程A拿到分布式锁后执行业务时间超过了expire时间，则线程B就能拿到分布式锁，但有一种情况是线程B拿到分布式锁还没执行完业务时线程A业务执行完毕del了分布式锁，由于线程A和线程B是同一把锁则造成了线程B在没有执行完毕时释放了锁，这样线程C进入业务时就会造成线程安全问题。

`问题核心：在线程A拿到锁执行业务时线程B把线程A的锁删掉了（线程删掉了其他线程的锁）`

* 解决方法：在存放锁时存放一个记录线程信息的value，在其他线程试图释放这个锁时判断value是否和自己的线程信息一致，否则该线程就是在尝试释放其他线程的锁。有效避免误删其他线程的锁而造成的线程安全问题。

---

**注意**

* 在使用分布式锁记录线程信息时要使用分布式锁+线程id or UUID+线程id。因为线程id是由jvm中栈记录的，在分布式系统中会造成id冲突。

### Lua保证多条命令原子性

> 避免极端情况下的线程安全问题。例：在线程准备释放分布式锁时服务宕机则释放动作失败造成超时误删，可以使用Lua语言使redis命令的原子性。

#### Lua基本语法

> 在脚本文件中使用redis.call()调用redis中的原始命令

```shell
# Lua脚本简单示例
redis.call('set', 'name', 'jack')
local name = redis.call('get', 'name')
return name
# 在命令行中调用Lua无参脚本
EVAL "return redis.call('set', 'name', 'jack')" 0
# 调用Lua有参脚本：1指的是KEYS有一个，也就是name为key、heihei则不是key
EVAL "return redis.call('set', KEYS[1], ARGV[1])" 1 name heihei
```

* 释放锁的Lua脚本实例。在resource下创建unlock.lua

  ```properties
  -- 比较线程表示与锁中的标识是否一致
  if (redis.call('get', KYES[1]) == ARGV[1]) then
      --释放锁
      return redis.call('del', KEYS[1])
  end
  return 0
  ```
  
* 在Java中调用Lua

  ```java
  private static final DefaultRedisScript<Object> UNLOCK_SCRIPT;
  
  private static final String ID_PREFIX = "threadId:";
  
  static {
      UNLOCK_SCRIPT = new DefaultRedisScript<>();
      UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));  //ClassPathResource为spring自带方法，寻找resource文件夹下的文件
      UNLOCK_SCRIPT.setResultType(Object.class);
  }
  //释放锁
  @Override
  public void unlock() {
      stringRedisTemplate.execute(UNLOCK_SCRIPT, Collections.singletonList(KEY_PREFIX + name), ID_PREFIX + Thread.currentThread().getId());
  }
  ```

`利用setnx满足互斥性、setex保证redis服务宕机锁依然能被释放，保证安全性、`

1. 互斥性：redis中setnx天然保持互斥性
2. 安全性：set ex设置过期时间保证服务宕机也能删除锁，保证安全性
3. 高可用：利用redis分布式集群保证高可用和高并发性

### Setnx实现的分布式锁中存在的问题

> 1. 不可重入性：不能在同一线程中获取同一把锁。如果在方法A中获取锁后，进入方法B还想获取锁则线程会陷入阻塞状态。
> 2. 不可重试：获取锁只尝试一次就返回false，没有重试机制。
> 3. 超时释放：锁超时虽然会避免死锁，但是业务耗时执行过长时可能会存在误删问题，存在安全隐患
> 4. 主从一致性：如果redis提供了主从集群，主从同步存在延迟，当主宕机时，如果从同步主忠的锁数据，则会出现多个线程拿到锁的问题。

### 秒杀异步优化

> 秒杀业务流程：判断库存是否充足 -> 判断用户是否下单

1. 将需要被秒杀的优惠券在存储到数据库时存储到缓存中。（类似将要被秒杀的商品）

2. 用Lua脚本判断用户的秒杀资格

   ```lua
   -- 1.参数列表
   -- 优惠券id
   local voucherId = ARGV[1]
   -- 用户券id
   local userId = ARGV[2]
   
   -- 2.数据key
   -- 库存key
   local stockKey = 'seckill:stock:' .. voucherId
   -- 订单key
   local orderKey = 'seckill:order' .. voucherId
   
   -- 3.脚本业务
   if (tonumber(redis.call('get', stockKey) <= 0) then
           -- 库存不足，返回1
           return 1
   end
   -- 判断用户是否重复下单
   if (redis.call('sismember', orderKey, userId) == 1) then
           --存在，说明重复下单
           return 2
   end
   -- 扣库存
   redis.call('incrby', stockKey, -1)
   -- 下单保存用户
   redis.call('sadd', orderKey, userId)
   return 0
   ```

![image-20230227144512709](lua秒杀下单脚本.png)

3. 执行lua脚本，判断是否有购买资格。0有1没有。

   ```java
   private static final DefaultRedisScript<Object> SCRIPT;
   
   public R seckillVoucher(Long voucherId) {
       Long userId = UserHolder.getUser().getId();
       //1.执行lua脚本
       Long result = stringRedisTemplate.execute(
           SCRIPT, 
           Collections.emptyList(),
           voucherId.toString(), 
           userId.toString()
       );
       //2.判断返回值是否为0
       int r = result.intValue();
       if (r != 0) {
           //2.1不为0，没有购买资格，直接返回
           return R.error(r == 1 ? "库存不足" : "不能重复下单");
       }
       //2.2为0，有购买资格，将保存订单操作加入到阻塞队列中消费
       long orderId = redisIdWorder.nextId("order");  //用工具类生成订单id
       //2.3将下单信息加入到阻塞队列
       VoucherOrder voucherOrder = new VoucherOrder();
       voucherOrder.setId(orderId);  //订单id
       voucherOrder.setUserId(userId);//下单用户id
       voucherOrder.setVoucherId(voucherId);//优惠券id
       proxy = (IvoucherOrderService) AopContext.currentProxy();  //拿到当前代理对象
       //加入阻塞队列
       orderTasks.add(voucherOrder);
       //3.返回订单id
       return R.ok().put(orderId);
   }
   
   
   private BolckingQueue<VoucherOrder> orderTasks = new ArrayBlockingQueue<>(1024 * 1024);
   private static final ExecutorService SECKILL_ORDER_EXECUTOR = Exercutors.newSingleThreadExecutor();
   
   //注解：在该方法所在类初始化时运行
   @PostConstruct
   private void init() {
       SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
   }
   
   
   private class VoucherOrderHandler implements Runnable{
       @Override
       public void run() {
           while(true) {
               //1.获取队列中的订单信息
               try {
                   VoucherOrder voucherOrder = orderTasks.take();
                   //2.创建订单
                   handleVoucherOrder(voucherOrder);  //创建订单方法
               } catch (Exception e) {
                   e.prntStackTrace();
               }
           }
       }
   }
   
   @Resource
   private RedissonClient redissonClient;
   
   private IvoucherOrderService proxy;
   
   //异步保存订单方法
   private void handleVoucherOrder(VoucherOrder voucherOrder) {
       //1.获取用户
       Long userId = voucherOrder.getUserId();
       //2.创建锁对象
       RLock lock = redissonClient.getLock("lock:order:" + userId);
       //3.获取分布式锁
       boolean isLock = lock.tryLock();
       if (!isLock) {
           log.error("不允许重复下单");  //兜底，重复下单在lua中已经判断了
           return;
       }
       try {
           proxy.createVoucherOrder(voucherOrder);  //拿到代理对象创建订单。
       } finally {
           lock.unlock();
       }
   }
   
   @Transaction
   private void createVoucherOrder(VoucherOrder voucherOrder) {
       //执行在数据库中创建订单逻辑
       save(voucherOrder);
   }
   ```

`该方法用到JVM阻塞队列来异步执行耗时较长的保存订单信息操作，坏处是在服务宕机时存储在内存中的数据都会丢失，会造成缓存一致性问题。`



## Redisson

> 在redis基础上实现的分布式组件库。是Java分布式系统中的常用对象，也提供了许多分布式服务。

`可重入锁、公平锁、联锁、红锁、读写锁、信号量、可过期性信号量、闭锁`

[官方地址]:https://redisson.org

```xml
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.13.6</version>
</dependency>
```

配置Redisson客户端：使用分布式锁时建议自己建立配置类

```java
import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.config.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RedisConfig {
    @Bean
    public RedissonClient redissonClient() {
        Config config = new Config();
        config.useSingleServer().setAddress("").setPassword("");
        return Redisson.create(config);
    }
}
```

### 可重入锁

> 通过Lua + Redis中Hash数据类型实现。key记录线程信息，value记录获取了多少把分布式锁，当value为 0 时表示所有线程都释放了分布式锁。

`redisson实现的锁机制和Java中ReentryLock机制类似，记录缩标识来表示获取了几把锁。`

* 获取锁的Lua脚本

```lua
local key = KEYS[1];  -- 锁key
local threadId = ARGV[1];  -- 线程Id
local releaseTime = ARGV[2];  -- 锁自动释放时间

-- 判断锁是否存在
if (redis.call('exists', key) == 0) then
    --不存在，获取锁。获取的第一把锁标识为 1
    redis.call('hset', key, threadId, '1');
    redis.call('expire', key, releaseTime);
    return 1;  --返回成功结果
end;
-- 如果锁已经存在，判断是否是当前线程id拿到了锁
if (redis.call('hexists', key, threadId) == 1) then
    -- 是当前线程
    redis.call('hincrby', key, threadId, '1');
    -- 设置有效期
    redis.call('expire', key, releaseTime);
    return 1;
end;
return 0;
-- 总体流程为 -> 判断是否有锁 -> 判断该锁是否为本线程所有 -> 是本线程所有则锁value + 1 -> 不是当前线程锁 -> 返回 0
```

* 释放锁的Lua脚本

```lua
local key = KEYS[1];
local threadId = ARGV[1];
local releaseTime = ARGV[2];

if (redis.call('hexists', key, threadId) == 0) then
    return nil;  --不是自己的锁直接返回nil
end;
local count = redis.call('hincrby', key, threadId, -1);  -- 是自己的锁，重入锁-1
if (count > 0) then
    -- 大于0时表示外层还有锁未释放，重置expire时间
    redis.call('expire', key, releaseTime);
    return nil;
else
    redis.call('del', key);  -- 小于等于0表示这是最外层锁，可以直接删除分布式su
    return nil;
end;
```

### 重试机制

> redissonClient.getLock("lock_name").tryLock(waitTime, releaseTime, unit);  -> 参数：1.重试拿锁循环时间 2.锁失效时间 3.设置时间单位

`默认锁超时释放时间30s `

* tryLock(waitTime, releaseTime, unit)：当releaseTime为-1时表示开启看门狗（默认30s有效期）
* 成功获取分布式锁流程
  * 尝试获取锁 -> 获取锁成功返回nil （相当于成功返回null，失败返回锁剩余expire时间）
  * 拿到锁后判断锁释放时间是否为-1，为-1则开启watchdog，然后返回true。
* 获取分布式锁失败流程
  * 尝试获取锁，返回null -> 判断剩余等待时间是否为0 -> 返回false
  * 并且subscript订阅等待目标线程释放信号量 -> 判断等待时间是否超过锁存活时间
  * 没有超过锁存活时间则再次尝试获取锁。否则直接返回false

`总体流程是获取锁 -> 成功 -> 开启watchdog -> 获取失败的锁在接收其他线程锁释放的信号量后重新尝试`

![image-20230223153914320](redisson原理.png)

### 主从一致

> 产生原因：在分布式redis中有master节点和多个slave节点。一般master节点做写操作slave做读操作，在master将数据同步到slave节点中时master节点宕机。这时redis会自动选出一个slave节点作为新的master节点，这样就造成了客户端请求将分布式锁请求打入了slave中，slave还没来得及同步master中的锁信息。造成了其他线程拿到分布式锁的情况。

`redisson解决方法：将所有redis节点都存入锁信息，只有在所有节点都获取到锁信息才算获取锁成功。`

* 联锁：MultiLock -> 只有在创建中的锁都获取成功了联锁才算获取成功

  ```java
  @Resource
  private RedissonClient redissonClient;
  
  RLock lock1 = redissonClient.getLock("lock1");
  RLock lock2 = redissonClient.getLock("lock2");
  RLock lock3 = redissonClient.getLock("lock3");
  RLock multiLock = redissonClient.getMultiLock(lock1, lock2, lock3);
  boolean tryLock = multiLock.tryLock(1, 10, TimeUnit.SECONDS);  //每1秒重试获取锁，锁在10秒后过期。
  if (tryLock) {
      //获取锁成功
  }
  ```

`主从一致主要是因为在多节点的Redis下有单节点服务宕机但刚好这个节点存储分布式锁相关信息时会造成分布式锁重复获取的情况。联锁有效避免了该情况的发生。`

## 消息队列

> 用redis实现消息队列。最简单分为三个角色：
>
> 1. 消息队列：存储和管理消息，被称为消息代理
> 2. 生产者：发送消息到消息队列
> 3. 消费者：从消息队列只能获取消息并处理消息

`解耦合、提高并发能力，并且记录消息不会被丢失。比用JVM实现的阻塞队列的好处是：高可用、高并发、持久化`

* list结构：基于list结构的模拟消息队列。优点：基于Redis存储，不受限于JVM内存上限。缺点：只接收单次消费。无法避免消息丢失

  ```shell
  # BRPOP [key] [time]:移除并获取最后一个元素，如果没有元素则阻塞等待。等待20s
  BRPOP l1 20
  
  # LPUSH :从左边存储一个元素，存储20到l1队列中
  LPUSH l1 20
  ```

* PubSub：基本点对点的消息模型。发布订阅机制消费者可以订阅一个or多个channel

  ```shell
  # subscribe channel [channel]：订阅一个or多个频道
  # publish channel msg：给一个频道发送消息
  # psubscribe pattern [pattern]：订阅与pattern格式匹配的所有频道
  ```

  ![image-20230227155651422](redis实现消息队列订阅模式.png)

  * 优点：多生产多消费，发布订阅模型
  * 缺点：不支持数据持久化，无法避免消息丢失（没人订阅的频道消息会直接丢失），消息堆积有上限超出时数据丢失。

* Stream：比较完善的消息队列模型（5.0后的新数据类型）

  * 发送消息

  ![image-20230227160703938](stream类型做消息队列常用参数.png)

  ```shell
  # xadd key [maxlen] *|id field value [field value]
  ## 创建一个名为users的队列，并向其中发送一个消息，内容是：{name=jack,age=21}，并且使用Redis自动生成ID
  例：xadd users * name jack age 21
  ```

  * 读取消息

  ![image-20230227161806360](stream读取消息.png)

  ```shell
  # xread 阻塞方式获取最新消息
  
  # 获取users消息队列中的最新消息
  xread count 1 block 1000 streams users $
  ```

  * 业务代码中常用：

    ```java
    while(true) {
        //尝试读取队列中的消息，但最多等2s。获取消息队列中1一个消息
        Object msg = redis.execute("xread count 1 block 2000 streams users $");  //可能会漏读消息
        if (msg == null) {
            continue;
        }
        //处理消息
        handleMessage(msg);
    }
    ```

    `当指定ID为$时表示读取最新消息，如果处理消息的过程中又有超过一条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现漏读消息的问题。`

  * 消息可回溯、一个消息可以被多个消费者读取、可以阻塞读取；有漏读消息的风险；

### 消费者组

> 将消费者划分到一个组中，监听同一个队列。
>
> 1. 消息分流：  消息组中同时争抢一个队列中的消息。提高处理消息速度
> 2. 消息标示：最后同一个被处理的消息会被标记，还会标识之后读取的消息，确保每个消息都会被消费。
> 3. 消息确认：消费者获取消息后，消息处于pending状态，并存入pending-list中。只有消息处理完毕后用XACK确认后的消息才会被移除。

* 创建消费者组：id起始坐标0为消息头，$为消息尾。[mkstream]：队列不存在时自动创建消息队列

  ```shell
  xgroup create key groupName id [mkstream]
  ```

* 其他命令

  ```shell
  # 删除消费者组
  xgroup destroy key groupName
  # 给指定的消费者组添加消费者
  xgroup createconsumer key groupName consumerName
  # 删除消费者组中的指定消费者
  sgroup delconsumer key groupName consumerName
  ```

* 从消费者组中读取消息

  ```shell
  XREADGROUP GROUP groupName consumerName [COUNT] [BLOCK milliseconds] STREAMS key [key ...] ID [ID ...] 
  ```

  * groupName：消费者组名称
  * consumerName：消费者名称，如果消费者不存在则自动创建
  * count：本次查询的最大数量
  * BLOCK milliseconds：当没有消息的最大等待时间
  * STREAMS key：指定队列的名称
  * ID：获取消息的启始ID：
    * ">"：从下一个未消费的消息开始
    * 其他：根据指定id从pending-list中获取**已消费但未确认的消息**，例如0，是从pending-list中的第一个消息开始

* xack：标记一个待处理的消息为已处理的消息

  ```shell
  # 标记后消息从pending-list中移除：返回已标记的消息数量，比如标记了3条消息则返回3
  xack key groupName ID [ID ...]
  ```

* xpending：指定范围标记已处理的消息

  ```shell
  # xpending key groupName [[idle min-idle-time] start end count [consumer]]
  xpending key groupName - + 10
  ```

*Java实例*

```java
while(true) {
    Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >");
    //从消费者组g1中用消费者c1读取队列key为s1的第一条未消费的消息
    if (msg == null) {
        continue;
    }
    
    try {
        //处理完消息后一定要进行xack处理
        handleMessage(msg);  //处理业务消息
    } catch(Exception e) {
        while(true) {
            Object msg = redis.call("XREADGROUP GROUP g1 c1 COUNT 1 STREAMS s1 0");
            //从第一条消息开始读取已消费但未确认的消息（在消费过程中出现异常的消息）
            if (msg == null) {
                break;  //null说明没有异常，消息都已确认，结束循环
            }
            try {
                //说明有异常消息，再次处理
                handleMessage(msg);
            } catch (Exception e) {
                //再次出现异常，记录日志，结束循环
                break;
            }
        }
    }
}
```

---

特点：

* 消息可回溯
* 可以多个消费者争抢消息，加快消费速度
* 可以阻塞读取
* 没有消息漏读的风险
* 有消息的确认机制，保证消息至少被消费一次

![image-20230227231507812](stream总结.png)

`stream依赖redis本身持久化，消息确认机制只能用于消费者的消费，不支持生产者消费的机制，不支持消费事务、消费的有序性等，如果需要使用上面的功能则需要使用更为完善的消息队列例如MQ、Kafka等。`

# 字节课程笔记

* 基本原理：
  * 客户端发送RESP协议访问redis服务 -> 将操作读写到内存中并将操作命令追加到AOF文件中，并压缩。
  * RDB文件中保存的是redis中所有的信息。redis先执行RDB文件同步数据再检查AOF中有没有指令没有执行。

![image-20230220212157386](redis读取流程.png)
