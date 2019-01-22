# NoSql



## 概述

* KV
* Cache
* Persistence



3V

* 海量 Volume
* 多样 Variety
* 实时 Velocity

3高

* 高并发
* 高扩展（扩展包括横向，纵向）
  * 高可扩就是横向
* 高性能



### 应用

阿里巴巴网站实例



多数据源 多数据类型的存储问题



* 商品基本信息：（趋冷数据）关系型数据库，淘宝内部用MySQL，修改后内核。
  * 去IOE化：去掉IBM小型机，Oracle数据库，EMC存储设备
* 商品描述、详情、评价信息（多文字类）：
  * 文档数据库MongoDB
* 商品的图片：分布式文件系统
  * TFS(淘宝)
  * Google的GFS
  * Hadoop的HDFS
* 商品的关键字：
  * ISearch
* 商品波段性的热点高频信息：内存数据库
  * Tair
  * Redis
  * Memcache
* 商品交易，价格计算，积分累计：
  * 外部系统、支付宝



总结：大型应用：

* 技术难点
  * 数据类型多样性
  * 数据源多样性和变化重构
  * 数据源改造而数据服务平台不需要大面积重构
* 解决方法
  * 统一数据平台服务平层（UDSL）
    * 映射
    * API
    * 热点缓存
    * 等



## NoSQL数据模型

### 例子

以一个电商客户、订单、订购、地址模型来对比关系型数据库与非关系型数据库

* 关系型数据库
  * ER图，1：1、1：N、N：M
* 非关系型数据库
  * BSON：Binary JSON，支持内嵌的文档对象和数组对象

```json
{
    "customer":{
        "id":123,
        "name":"abc",
        "address":[{"city":"beijing"}],
        "order":[
            {
                "id":44;
                "customerID"：123,
                orderItems:[{"productID":11,"price":22}]
            }
        ]
    }
}
```



问题

* 聚合模型
  * 高并发操作不建议有 关联查询
    * 互联网公司用 冗余数据来避免关联查询
  * 分布式事务 支持不了太多的并发



聚合模型

* K-V
* BSON
* 列族
* 图形：社交网络



## NoSQL数据库四大分类



* KV键值
  * 新浪：BerkeleyDB + redis
  * 美团：redis + tair
  * 阿里，百度：memcache + redis
* 文档型数据库 BSON
  * MongoDB：分布式文件存储的数据库
  * 介于关系型数据库与非关系型数据库
* 列存储数据库
  * HBase
  * 分布式文件系统
* 图关系数据库
  * Neo4J，InfoGrid
  * 社交网络，推荐系统。关系图谱
* 对比



## 分布式数据库CAP原理



### 传统数据库的ACID

* A 原子性
* C 一致性
* I 独立性
* D 持久性



### CAP

* C 强一致性
* A 高可用性
* P 分区容错性（分布式）



CAP 三选二

* CA：一致性，高可用性
  * 传统Oracle数据库
* CP：一致性
  * Redis，MongoDB
* AP：高可用性，容错性
  * 大多数网站架构



电商：弱一致性+AP



一致性：



实时性：



### BASE：

* 基本可用（basically available
* 软状态（soft state
* 最终一致（eventually consistent

基本思想：让系统放松对某一时刻 数据一致性 的要求 来换取系统整体伸缩性和性能上的改观。

牺牲C 来 换取AP



### 分布式 + 集群



分布式：不同服务器上部署不同的模块

集群：不同服务器上部署相同模块





## Redis

### Redis概述

Redis：REmote DIctionary Server（远程字典服务器）

特点

* redis支持持久化
* 不仅支持kv类型，还支持list，set，zset，hash
* 支持数据备份，即master-slave模式

能干什么

* 内存存储和持久化
* 取最新N个数据的操作
* 发布、订阅消息系统

怎么玩

* 数据类型，基本操作和配置
* 持久化和复制，RDB/AOF
* 事务的控制
* 主从复制



Linux版Redis安装

* tar -zxvf redis-3.0.4
* cd redis-3.0.4
* 安装gcc
  * yum install gcc-c++
  * 不能上网
* make
  * 出现：Jemalloc/jemalloc.h没有文件目录
    * 运行make distclean之后再make
* 



Redis test最好不要测试



### redis启动后杂项





```bash
ln -s /data/redis-5.0.2/src/redis-server /usr/bin/redis-server
ln -s /data/redis-5.0.2/src/redis-cli /usr/bin/redis-cli
ln -s /data/redis-5.0.2/src/redis-benchmark /usr/bin/redis-benchmark

# 开启redis
redis-server /data/redis-5.0.2/redis.conf
redis-cli -p 6379



# 测试redis-benchmark

[oracle@zookeeper ~]$ redis-benchmark 
====== PING_INLINE ======
  100000 requests completed in 1.87 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

99.94% <= 1 milliseconds
100.00% <= 1 milliseconds
53619.30 requests per second

====== PING_BULK ======
  100000 requests completed in 1.86 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
53763.44 requests per second

====== SET ======
  100000 requests completed in 1.88 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

99.95% <= 1 milliseconds
99.97% <= 2 milliseconds
100.00% <= 2 milliseconds
53219.80 requests per second

====== GET ======
  100000 requests completed in 1.87 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
53390.28 requests per second

====== INCR ======
  100000 requests completed in 1.86 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
53879.31 requests per second

====== LPUSH ======
  100000 requests completed in 1.84 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
54406.96 requests per second

====== RPUSH ======
  100000 requests completed in 1.83 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
54525.63 requests per second

====== LPOP ======
  100000 requests completed in 1.84 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
54466.23 requests per second

====== RPOP ======
  100000 requests completed in 1.87 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
53619.30 requests per second

====== SADD ======
  100000 requests completed in 1.86 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
53908.36 requests per second

====== HSET ======
  100000 requests completed in 1.84 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

99.98% <= 1 milliseconds
100.00% <= 1 milliseconds
54377.38 requests per second

====== SPOP ======
  100000 requests completed in 1.85 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
54200.54 requests per second

====== LPUSH (needed to benchmark LRANGE) ======
  100000 requests completed in 1.83 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

100.00% <= 0 milliseconds
54585.15 requests per second

====== LRANGE_100 (first 100 elements) ======
  100000 requests completed in 3.18 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

99.89% <= 1 milliseconds
100.00% <= 1 milliseconds
31436.65 requests per second

====== LRANGE_300 (first 300 elements) ======
  100000 requests completed in 6.98 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

0.01% <= 1 milliseconds
97.96% <= 2 milliseconds
99.99% <= 3 milliseconds
100.00% <= 4 milliseconds
14318.44 requests per second

====== LRANGE_500 (first 450 elements) ======
  100000 requests completed in 9.62 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

0.00% <= 1 milliseconds
0.25% <= 2 milliseconds
99.95% <= 3 milliseconds
99.98% <= 4 milliseconds
100.00% <= 5 milliseconds
100.00% <= 5 milliseconds
10398.25 requests per second

====== LRANGE_600 (first 600 elements) ======
  100000 requests completed in 12.18 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

0.00% <= 1 milliseconds
0.01% <= 2 milliseconds
48.25% <= 3 milliseconds
99.93% <= 4 milliseconds
99.96% <= 5 milliseconds
99.97% <= 6 milliseconds
99.97% <= 7 milliseconds
99.99% <= 8 milliseconds
100.00% <= 9 milliseconds
8212.88 requests per second

====== MSET (10 keys) ======
  100000 requests completed in 1.82 seconds
  50 parallel clients
  3 bytes payload
  keep alive: 1

99.94% <= 1 milliseconds
100.00% <= 1 milliseconds
54854.64 requests per second
```

#### 单进程

单进程，通过epoll函数包装。

Redis实际处理速度完全依赖主进程的执行效率

Epoll是linux内核为处理大批量文件描述而作了改进的epoll。







默认16个库

* 0-15
* select NUM 切换数据库



```bash
>dbsize   #查看当前库的key数量
>4
```



* flushdb 清空当前库
* flushall 清空16个库
* 统一密码管理，16个库都是同样密码。
* redis索引都是从零开始
* 默认端口是6379



## Redis 数据类型



5大数据类型

* String(字符串)
  * key value
  * 二进制安全
  * 一个redis字符串value最多可以512M
* Hash（类似java里的map）
  * 键值对
  * 一个string类型的field和value的映射表，hash特别适合用于存储对象
  * 类似Java里的Map<String, Object>
* List(列表)
  * 简单的字符串列表，按照插入顺序
* Set(集合)
  * 无序集合，通过HashTable实现
* Zset(sorted set,有序集合)
  * 每个元素都会关联一个double类型的分数



常用命令参考

http://redisdoc.com



* key
  * keys *
  * exists key 判断某个key是否存在
  * move key dbnum 把当前库移到其他库
  * ttl key 查看还有多少秒过期，-1永不过期，-2已过期
    * 过期 keys 就找不到了
  * type key 查看key的类型
* String
* list
* set
* hash
* zset





## 解析配置文件 redis.conf



windows里 E:\Program Files (x86)\Redis-x64-3.2.100\redis.windows.conf

linux里 /data/redis-5.0.2/redis.conf





linux中配置文件大于编码



备份配置文件



### includes





### general

* daemonize yes
* pidfile
* port 6379
* tcp-backlog 511，backlog其实是一个连接队列，backlog队列总和=未完成三次握手队列+
* timeout 0
* tcp-keeplive 0 单位秒
* loglevel 日志级别
* logfile  “ ”
* databases 16



### snapshotting 快照



### security

config get requirepass 

config set requirepass "new_pw"



auth "new_pw"



### limits

* maxclients  10000
* maxmemory  <bytes> 
  * 缓存过期的配置
* maxmemory-policy
  * 缓存策略
  * 5种
    * volatile-lru:(lru:less recently use) 根据最近最少使用算法，移除设置了过期时间的key
    * allkeys-lru:根据lru移除
    * Volatile-random：随机移除过期集合里的key
    * Allkeys-random：移除随机的key
    * Volatile-ttl：移除ttl值最小的key，即那些最近要过期的key
* maxmemory-policy Noeviction ：永不过期



## 持久化

* rdb (redis database)
* aof (append only file)



### RDB(Redis DataBase)



在指定时间间隔内将内存的数据集快照写入磁盘，snapshot快照，



redis单独创建(fork)一个子进程进行持久化，会将数据写入到一个临时文件里，待持久化结束后，再用这个临时文件替换上次持久化好的文件。

整个过程，主进程不进行任何IO操作，确保了极高性能

如果要大规模恢复，且数据恢复的完整性不是很敏感，那么RDB。RDB的缺点是最后一次持久化后的数据可能会丢失。



fork 复制一个与当前进程一样的进程。



dump.rdb 恢复策略

```bash
Save the DB disk:
	save <seconds> <changes>
	save 300 10
	# 300秒 10次写了key
```









* save
* bgsave



* 优势
  * 适合大规模
  * 对数据完整性和一致性要求不高
* 劣势
  * 最后一次快照可能存不进去
  * fork两倍

### AOF（Append Only File）

以日志的形式来记录每个写操作，将Redis执行过的所有写指令记录下来（读操作不做记录），只许追加文件但不修改文件。



配置文件：

redis.conf

APPEND ONLY MODE





appendonly.aof与dump.rdb可以共存（没有问题的时候），且先读appendonly.aof，如果appendonly.aof内容错误，连接redis会失败



如何修复：

redis-check-aof --fix appendonly.aof

删除掉不符合语法规范的最后一次输入的命令



### append only mode追加

* 默认appendonly no，需要改成yes、
  * 将数据的aof文件复制到对应目录下（config get dir) 备份机器上。
  * 重启redis然后重新加载
* appendfilename
* Appendfsync
  * always:同步持久化，每次发生数据变更就立即记录到磁盘，性能较差，但数据完整性高
  * everysec:默认配置，异步操作，每秒记录，如果一秒内宕机，有数据丢失。
  * no









* Rewrite

  * 是什么：AOF采用文件追加，文件会越来越大，为避免这种情况，新增重写机制，当AOF文件的大小超过设定的阈值，Redis会启动AOF文件的内容压缩。只保留可以恢复数据的最小指令集。

  * 重写原理：AOF文件持续增加而增大，会fork出一条

  * 触发机制：Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后代销的一倍且文件大于64M时触发。

    ```bash
    auto-aof-rewrite-percentage 100  #设置重写的基准值
    auto-aof-rewrite-min-size 64mb  #设置重写的基准值
    
    #生产环境3g起步
    ```



劣势：

* 相同数据集的数据 aof文件远大于rdb文件，恢复速度慢于rdb
* aof运行效率慢于rdb，每秒同步策略效率要好，不同步效率和rdb相同



官方建议：

* RDB持久化能够在指定时间间隔能对你的数据进行快照存储
* AOF持久化方式记录每次对服务器写的操作，当服务器重启的时候回重新执行这些命令来恢复数据，AOF命令以redis协议追加保存每次的写操作到文件末尾。Redis还能对AOF文件进行后台重写，使得AOF问价你的体积不至于过大
* 只做缓存：如果只希望数据在服务器运行时存在，可以不是用任何持久化
* 同时开启两种持久化方式：
  * 这种情况下，Redis重启后会优先载入AOF文件恢复数据。因为通常情况AOF文件保存的数据要比RDB文件更完整。
  * RDB数据不实时，同时使用两者，服务器也会只找AOF文件。RDB更适合备份数据库(AOF在不断变化不好备份)，快速重启，而且不会有AOF潜在的bug。
* 性能建议：
  * 因为RDB文件只用作后备用途，建议在Slave上持久化RDB文件，而且15分钟备份一次就够了。
  * 如果Enable AOF，好处是最恶劣情况下也只会丢失不超过两秒数据，启动脚本只load自己的AOF文件即可。代价是：一是带来持续的IO，二是AOF rewrite的最后将rewrite过程中产生的系数据写到新文件造成的阻塞几乎不可避免。应尽量减少AOF rewrite的频率，AOF重写的基础可以设置5G以上。
  * 如果不Enable AOF，仅靠Master-Slave Replication实现高可用也可以。代价是如果Master Slave挂掉，







## Redis 事务



概念： 

​	可以一次执行多个命令，本质是一组命令的集合。一个事务中，所有命令都会序列化，按照顺序串行化执行而不会被其他命令插入，不许加塞。



一个队列中，一次性，顺序性，排他性的执行一系列命令



常用命令：

* discard：取消事务
* exec：执行所有事务块内的命令
* multi：标记一个事务块的开始
* unwatch：取消watch命令对所有key的监视
* watch key[key ...]: 监视一个或多个key，如果事务执行前这个key被其他命令改动，则事务被打断。





* 正常执行：

* 放弃事务：

* 全体连坐：

  * 输入错误的命令就都不行

* 冤头债主：

  * 语法对的，显示放到队列里了，

* watch监控：

  * 悲观锁、乐观锁、CAS（check and set）

    * 悲观锁：锁整张表
      * 每次取数据的时候，都会有人修改，所以锁整张表。性能不好（数据库备份的时候使用）
    * 乐观锁（用的多）：最后加了个version字段，
      * 取数据的时候没人修改，不会上锁。更新数据，添加上version。
      * 提交版本必须大于记录当前版本才可以执行
    * CAS：

  * 示例：

    ```bash
    > set balance 100
    > set debt 0
    > WATCH balance (监控)
    > MULTI (标记事务的开始)
    > decrby balance 20 (减 20)
    > incrby debt 20 (加 20)
    > EXEC (执行事务)
    (integer) 80
    (integer) 20
    > get balance
    "800"
    > get debt
    "20"
    > WATCH balance
    > set balance 500
    > UNWATCH
    > WATCH balance
    > MULTI
    > set balance 80
    QUEUED
    > set debt 20
    QUEUED
    > EXEC
    OK
    OK
    # 改东西的时候 要加watch
    # 把新数据拿出来 再改
    ```

    

```bash
> set k1 v1
QUEUED
> set k2 v2
QUEUED
> EXEC

```

对事务 部分支持



一旦执行了EXEC之后，之前加的监控锁就会被取消。



总结：

* watch相当于乐观锁





事务3阶段

* 





事务3特性

* 单独隔离操作：操作序列化顺序执行
* 没有隔离级别概念：
* 不保证原子性：



## Redis 发布与订阅

概念： 进程间的一种消息通信模式

* 发送者 pub
* 订阅者 sub

 

先订阅后发布。

* 一次订阅多个 subscribe c1 c2 c3
* 消息发布 publish c2 hello_redis
* 订阅多个，通配符 * ，PSUBSCRIBE new*
* 收取消息，publish new1 redis2015







## Redis的复制（Master Slave）



主从复制



* 读写分离
* 容灾恢复



如何操作

* 配从库 不配主库

* 从库配置的命令： slaveof 主库IP 主库端口

  * 每次与master断开后，需要重新连接，除非配置进redis.conf文件
  * Info replication 配置信息

* 修改配置文件细节操作

  * 拷贝多个redis.conf文件

  * 开启daemonize yes

  * PID文件名字

  * 指定端口

  * log文件名字

  * dump.rdb名字

  * 例子

    * cp redis.conf redis6379.conf

    * vim red is6379.conf

    * pidfile /var/run/redis6379.pid

    * logfile "6379.log"

    * loglevel notice

      ------

    * cp redis.conf redis6380.conf

    * vim redis6380.conf

    * port 6380

    * pidfile /var/run/redis6380.pid

    * logfile "6380.log"

    * loglevel notice

* 常用：

  * 一主二从

    * init

      * ```bash
        # port：6379
        redis-server ../redis6379.conf
        redis-cli -p 6379
        > info replication 
        role:master
        > set k1 v1
        > keys *
        > 
        # port：6380
        redis-server ../redis6380.conf
        redis-cli -p 6380
        > info replication
        role:master
        > slaveof 127.0.0.1 6379
        > 
        # port：6381
        redis-server ../redis6381.conf
        redis-cli -p 6381
        > info replication
        role:master
        > slaveof 127.0.0.1 6379
        > 
        ```

      * 只要slaveof之后，slave就从头开始同步master上的数据

      * 在slave上不可以set

      * master当掉，slave没有变化，原地待命

      * master恢复了，依旧照旧，自动连接上

      * slave当掉（没写入配置文件），该slave恢复后，上级为master，等待其他的连接。如果再输入slaveof 127.0.0.1 6379后，则重新连上原来的master

    * 一个master 两个slave

    * 日志查看

  * 薪火相传

    * 去中心化，树型结构
    * 上一个slave是下一个slave的master，salve同样可以接受其他slaves的连接请求
    * 中途变更: 会清除掉之前的数据，重新建立 拷贝最新的

  * 反客为主

    * slave上执行slaveof no one 则成为master（使当前数据库停止与其他数据库的同步，转成主数据库）
    * 原master恢复后

* 复制原理

  * slave启动后，成功连接master后会发送一sync命令
  * master接到命令后启动后台的存盘进程，同时手机所有接收到的用于修改数据集的命令，在后台进程执行完毕后，master将传送整个文件到slave，以完成一次完整的同步
  * 全量复制（第一次）：slave服务接收到数据库文件后，将其存盘并加载到内存中
  * 增量复制（之后）：master继续将新的所有收集到的修改命令一次传给slave，完成同步
  * 但是只要重新连接master，一次完全同步（全量复制）将被自动执行

* 哨兵模式

  * 反客为主的自动版，后台监控主机是否故障，如果故障了根据投票自动将从库转换为主库
  * 步骤:
    * 结构：一主两从
    * 自定义sentinel.conf 文件
      * sentinel monitor mastername masterIP masterport 票数
    * 启动哨兵
      * redis-sentinel /usr/common/sentinel.conf
      * 目录根据实际情况
    * 原master恢复后，则变成为slave
  * 一组sentinel能监控多个master

* 缺点：有延迟  slave越多越明显

















