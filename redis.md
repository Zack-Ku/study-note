# Redis特性
单线程，避免线程开销。数据持久化

# 数据结构
list：rpush（推入最右），lrange（指定范围所有值）、lindex（获取指定位置元素）、lpop（弹出最左值）
set：sadd、smembers（返回所有元素）、sismember（是否存在）、srem（移除）
hash：hset、hget、hgetall、hdel
zset：

# 使用场景
微博点赞数用自增
微博消息列表

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

# Sentinel


# 集群Cluster
