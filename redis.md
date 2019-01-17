# Redis特性
1. 速度快。单线程，避免线程开销。
2. 丰富数据类型（Memcached仅支持字符串）
3. 支持事务、数据持久化（Memcached不支持），时间过期等特性
4. 自带失效策略

# 数据结构
String：get、set、del、INCR
普通使用、点赞自增等

list：rpush（推入最右），lrange（指定范围所有值）、lindex（获取指定位置元素）、lpop（弹出最左值）
用于消息队列。用户收到的最新评论、最新消息

set：sadd、smembers（返回所有元素）、sismember（是否存在）、srem（移除）
用于去重，交集，并集等运算

hash：hset、hget、hgetall、hdel
用于用户信息存储，

zset（sort set有序集合）：zadd、zrange、zrem、zincrby... 带分数操作，可以排序
排行榜zrank，列表排序

geo：经纬度

expireat key tim
redis 可以给key加上过期时间

# RDB 
https://zackku.com/redis-rdb-aof/

RDB，全量数据快照。SAVE/BGSAVE 或 有Key变更后定时执行。默认打开
SAVE会阻塞进程，推荐BGSAVE fork一个新进程，但会消耗更多内存。

优点：相同数据量下，通常文件体积较小，大量数据加载下会更快。
缺点：不及时保存有断电丢失数据风险；fork进程去保存数据耗时较长，消耗CPU。虽然AOF也是fork进程，但AOF保存时间短

# AOF
AOF，文件追加，操作日志记录。默认关闭。加载时相当于重新操作日志记录。与RDB可以共存。

保存策略，调用系统函数fsync().
1. appendfsync always，每次操作记录都同步到硬盘上，最低效，最安全。
2. appendfsync everysec，每秒执行一次把操作记录同步到硬盘上。默认选项。
3. appendfsync no，不执行fysnc调用，让操作系统自动操作把缓存数据写到硬盘上，不可靠，但最快。

优先加载AOF。

日志重写。默认大于64M时，增长一倍时重写。压缩相同key值操作。减少冗余

优点：高效、可靠；可重写压缩；文件易读
缺点：相同数据量下，通常文件体积较大；大量写入和载入时，效率较低

文件解析： *2代表两个参数 $1代表接下来的值有多少个字符

# 不小心flushall操作
马上执行shutdown nosave，避免redis aof重写。然后马上加载恢复aof文件

# Sentinel——哨兵模式
https://zackku.com/redis-sentinel/

用于故障转移。

1. 把Redis用Sentinel的模式启动，并初始化
2. 读配置，初始化sentienl master属性，主要是主库信息
3. 向主库建立链接。一条命令链接，一条订阅链接
4. 默认10秒一次向主库发消息，获取主库以及对应从库消息
5. 发现从库，则和从库同样建立两个链接
6. 默认两秒一次对频道发送自身sentinel和主库信息
7. 因为sentinel也订阅了hello频道，所以会发现其他sentinel，然后与其创建对应链接。
故障后
8. 默认一秒一次监控主从是否没回复，若没回复，则Sentinel自己将其主观下线
9. 询问其他sentinel是否认为也下线，投票数可配。超过则客观下线
10. 选举一个sentinel leader来执行故障转移
11. 按照库优先级、偏移量、id等选出主库。调用SLAVEOF no one升级为master
12. 让其他从库复制新主库
13. 后面旧主库上线后，变成新主库的从库

# 一致性哈希算法
环形，把实例的hash值作为节点，把hash值顺延到最近一个节点上出力。
缺点：如果实例hash分配不均会导致过多请求hash进入到某些实例上。

# 分片集群Cluster
https://zackku.com/redis-cluster/

采用槽位分配，16384个哈希槽位。需要手工配完槽位才能正常工作。

cluster meet 与其他服务连接。

# 缓存穿透、雪崩等问题
缓存穿透：请求缓存不存在的数据，落到db上。
解决：1.用互斥锁排队，对缓存为空的key加锁；2.布隆过滤器，立即找出是否存在

缓存雪崩：缓存在同一时间大量键失效，然后同时一瞬间来大量请求落到db中
解决：1.同上加锁排队；2.备份缓存，一份超时A,一份不超时B，A失效则读B，然后同时更新AB；3.随机加上超时时间，避免同一时间失效




