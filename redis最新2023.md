# Redis命令

### 基础命令

数据库的切换：select [index]	例如：select 8

查看当前库的key数量：dbsize

删除当前库的所有数据：flushdb	!慎用

删除所有库的数据：flushall	!慎用

退出：quit	或者	exit

关闭服务器：shutdown

### key操作命令

**keys [查询的条件，支持模糊查询]**

例如：keys *  、keys hi? 、keys *m 等

说明：keys的速度非常快，但是在一个大的数据库中使用它可能会阻塞当前服务器的服务，所以一般生产环境使用scan命令代替

**exists [key]**

判断一个key是否存在

**del [keys]** 

删除key	例如：del name age sex

**rename [key] [newname**]

重命名key为newname

**move [key] [db]**

移动key到指定的db库中

**type [key]**

返回key的类型

**expire与pexpire**

为key设置生存时间。当key的生存时间为0时，它会自动删除。expire的时间单位为秒，pexpire的时间单位为毫秒，rename操作不会改变key的生存时间。如果key不存在会返回0

例如：expire name 10 、pexpire age 1000

**ttl与pttl**

返回指定key的剩余生存时间，如果key已经不存在，则返回-2。当key存在但是没有剩余生存时间或没有设置生存时间时返回-1，否则返回剩余的生存时间。ttl是返回秒级单位，pttl是返回毫秒级单位

**persist**

去除指定key的生存时间，将这个key从易失去的变为持久的

**randomkey**

随机返回一个key，当数据库不为空时随机返回一个key，为空时返回nil

**scan [index] [match 检索的条件] [count 截止index] [type key的类型]**

例如：scan 0 match *na count 8 type string

match 和type 可以省略，scan 0 count 8

scan 命令会返回两部分，第一部分1）是下次开始的下标，2）第二部分是查到的内容

当数据量很大时，count的数量指定可能不会起作用，redis会自动调整每次的遍历数目

使用非法的增量游标不会造成服务器的奔溃

> 【扩展】

hscan:属于Hash型Value操作命令集合，用于遍历当前db中指定Hash表所有fileid-value对

sscan:属于Set型Value操作命令集合，用于遍历当前db中指定set集合的所有元素

zscan:属于ZSet型Value操作命令集合，用于遍历当前db中指定有序集合的所有数值和元素值

### String型Value操作命令

string类型的value可以存放任意数据，一个string类型的value最大是512M大小

**set**

set [key] [value],可以直接在set的时候设置过期时间，set name admin ex 200 表示name这个key在200s后会失效

同样的还有

》px [milliseconds]:为当前key设置过期时间，单位为毫秒

》nx:指定的key不存在才会成功，用于添加指定的key，等同于setnx命令

》xx：指定的key必须存在才会设置成功，用于更新指定key的value

说明：如果value字符串中带有空格，则该字符串需要使用单引号或双引号引起来，否则会认为set命令的参数数量不正确而报错

**setex 与 psetex**

setex name 200 cpw,设置当前key name的过期时间为200s，等价于set name cpw ex 200

psetex是设置过期时间为毫秒

**getset [key] [newvalue]**

获取到指定的key值并且重新赋值newvalue

**mset与msetnx**

mset k1 v1 k2 v2

同时设置一个或者多个key-value对，操作具有原子性，如果一个key已经存在，mset会覆盖原来的内容，msetnx只会在所有key都不存在的情况下进行赋值操作

**mget**

根据指定key同时获取多个值

mget k1 k2

**append**

追加值到指定的key的value

例如：append address sichua,得到的结果就是chinasichuan（原本的address为china）

**incr和decr**

数值增减一，incr key和decr key

**incrby和decrby**

规定步长的增加和减小

incrby [key] [num] ，例如：incrby age 5

》浮点数加->incrbyfloat age 2.5 	[ 没有decrbyfloat的说法，可以是incrbyfloat age -2.5 ]

**其他**

strlen [key]  ：获取字符串key的长度

getrange [key] [index] [num] ：获取key规定范围的值

setrange [key] [index] [value]：替换inex游标处开始的字符内容，如果游标前都是为空，则会使用\x00填充

### Hash型Value操作命令

redis本身就是一个key-value的形式存储数据。Hash表就是一个映射表map，也是由键-值组成，为了与整体key区分，这里的键被称为fieId，file-value均为String类型

**hset**

语法为hset key fieId value [fieId value]，例如hset person name chb age 23 sex 2，hset同hmset一样。hmget key fieId fieId [fieId] 获取多个fie的指定值，hgetall key 获取该key对应的所有fieId-value。hget key file 获取单个file的value。可以使用hlen key的形式获取key对象的键值对个数。hstrlen key fieId 获取key对象下指定fieId的value的字符串长度。获取所有的键：hkeys key；获取所有的值：hvals key。

### List型Value操作命令

redis存储数据的value可以是一个String列表类型数据，即该列表中的每个元素均为String数据类型，列表中的数据会按照插入顺序进行排序。不过，该列表的底层实际上是一个无头节点的双向链表，所以对列表表头与表尾的操作性能较高，但对中间元素的插入与删除操作的性能比较差。

**lpush/rpush**

lpush key element [element ...]	列表左/头部的插入方式

rpush key element [element ...]	列表右/尾部的插入方式

lrange key index01 index02	表示取key下标范围从index01到index02的值

lindex key index 表示取key下表为index的单个值

lset key index value 表示设定list为key的对象下标为index的值为value（会覆盖原先的值）

rpush key value 表示如果列表存在则在相应位置填入值

linsert key before|after pivot element 代表在key对象下的pivot前或者后插入element

lpop key count 表示从key列表对象的左侧弹出count个值，不写默认为1个

blpop key1 key2 ... counts秒 表示如果有key1 则从左侧弹出一个值，没有走下一个，直到有key存在或者遍历结束。如果有key存在值，弹出一个值并阻塞counts秒
rpoplpush key1 key2 表示从key1的尾部取一个值放到key2的左侧

lrem key count value 表示从key中移除count个value，count为正从头开始，count为负从尾开始

ltrim key index1 index2 表示截取index1到index2的元素为新的key列表

### Set型Value操作命令

redis中的Set集合与java中的Set集合相似，其底层都是value为null的hash表。也正因为如此，才会引发无序性和不可重复性

**操作命令**

sadd key element ... 添加多个元素

smembers key 展示key集合下的所有元素

scard key 返回key集合的元素数量

sismember key member 判断member元素是不是key集合的存在元素

smove key1 key2 member 移动元素member从key1到key2

srem key m1 m2 m3 ... 删除多个元素m1 m2 m3

srandmember key [count] 随机返回集合key中的count个元素，不写默认为1，如果为负数，可能会返回重复的元素

spop key [count] 随机移除key集合中的count个元素,返回随机的count个元素

sdiff k1 k2 差集，等于k1集合减去k2集合，这里的k1,k2先后顺序也会改变结果

sdiffstore key k1 k2 得到k1减k2的差集并放进key集合中

sinter k1 k2 求交集，同理sinterstore

sunion k1 k2 求并集，同理sunionstore

### ZSet有序集合的操作命令

ZSet是有序的集合但是也不能重复，底层是hashtable

**操作命令**

zadd key score member ... 添加权重或分值为score的元素member在集合key中，根据score从小到大排序

zrange key start end 获取范围小标从start到end的值

zrangebyscore key min max [withscores] [limit x x] 表示通过分值来获取范围在min到max之前的元素，并做分页，加了withscores会在显示结果的时候显示分值。min 为 -inf，max 为+inf的时候表示取向两个无穷。如果加(min，或者(max这种括号的形式表示开区间。

zcard key 返回key的长度

zcount key min max 返回分值在min到max之间的元素

zscore key member 返回key中member元素的分值

zrank key member 返回key中member元素的排名【从零开始】，zrevrank/ 反向排名同理

zrem key member [member] 移除一个或者多个

zremrangebyrank key start end 移除排名在start到end之间的

zremrangebyscore key min end 通过score移除

### benchmark性能测试

redis-benchmark -a 221700 -c 200 -n 20000

redis 性能测试工具可选参数如下所示：

| 序号 | 选项               | 描述                                       | 默认值    |
| ---- | ------------------ | ------------------------------------------ | --------- |
| 1    | -h                 | 指定服务器主机名                           | 127.0.0.1 |
| 2    | -p                 | 指定服务器端口                             | 6379      |
| 3    | -s                 | 指定服务器 socket                          |           |
| 4    | -c                 | 指定并发连接数                             | 50        |
| 5    | -n                 | 指定请求数                                 | 10000     |
| 6    | -d                 | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | -k                 | 1=keep alive 0=reconnect                   | 1         |
| 8    | -r                 | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | -P                 | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | -q                 | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | --csv              | 以 CSV 格式输出                            |           |
| 12   | -l（L 的小写字母） | 生成循环，永久执行测试                     |           |
| 13   | -t                 | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | -I（i 的大写字母） | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |

压测需要一段时间，因为它需要依次压测多个命令的结果，如：get、set、incr、lpush等等，所以我们需要耐心等待，如果只需要压测某个命令，如：set，那么可以在以上的命令后加一个参数-t set

### BitMap

一般应用于大数据量的二值性统计

**命令**

setbit key offset value 设定key的offset值为value，offset的最大值是14亿减一，value的值只能是0或者1

getbit key offset 获取offset的值

bitcount key [start end] 统计个数默认所有

bitpos key bit 返回第一个出现bit的offset

bitop operation destkey key [key]

​	>operation的值可以是and、or、not、xor这四种操作中的任意一种

### HyperLogLog

超级日志记录。数据类型可以理解为一个简单的set集合，集合元素为字符串。实际上是一种基数计算概率算法。

**命令**

pfadd key value ... 增加值到key对象中

pfcount key 统计，作用于单个key时，返回指定key的HyperLogLog集合的近似基数；作用于多个key时，返回所有key的并集的近似基数

pfmerge destkey sourcekey [sourcekey ...] 将多个HyperLogLog集合合并为一个HyperLogLog集合，并存储到destkey中，合并后的HyperLogLog的基数接近于所有sourcekey的HyperLogLog集合的并集

### Geospatial操作命令

geospatial，地理空间。

数据类型仍是一种集合，特殊的是由三部分组成的数据结构，称为空间元素；

--经度：longitude。有效经度[-180,180]。正的表示东经，负的表示西经。

--纬度：latitude。有效纬度为[-85.05112878,85.05112878]。正的表示北纬，负的表示南纬。

--位置名称：空间元素名称。

**操作命令**

geoadd key lo la member [...] 将一个或者多个空间元素添加到指定集合中。

geopos key member [...] 从指定的地理空间中返回指定元素的位置，即经纬度。

geodist key member1 member2 [unit] 计算m1,m2之间的距离，其中unit必须是m、km、mi（英里）、ft（英尺），计算时会假想地球是一个完美的球体，这一假想最大会造成0.5%的差距

geohash key member [member ...] 返回一个或者多个位置元素的Geohash值（Geohash是一种地理编码）

georadius key lo la radius unit [count num desc]返回半径radius范围内的坐标中心的元素

georadiusbymember key member radius unit [count num desc] 同理

### 发布/订阅命令

https://www.bilibili.com/video/BV1U24y1y7jF/?p=46&spm_id_from=pageDriver&vd_source=ac5b6d114bb50b5a2284f301ea2bd5bf

### Redis事务

本质是一组命令的批处理，这组命令在执行过程中会被顺序地、一次性全部执行完毕。redis的事务仅保证了数据的一致性，不具备原子性。通过乐观锁机制实现了简单的隔离性。没有复杂的隔离级别。

muti 开启事务

exec 执行事务

discard 取消事务

## Redis持久化

### 持久化原理

redis具有持久化功能，其会按照设置以快照的或者日志的形式将数据持久化到磁盘。根据使用的技术不同，redis的持久化分为两种：RDB与AOF。

### RDB持久化

RDB默认是开启的，当redis启动时会自动读取RDB快照文件，将数据从硬盘载入到内存，以恢复redis关机前的数据库状态。

执行方式：手动save命令、手动bgsave命令，与自动条件触发；

1.手动save命令

save

执行save命令会立即进行一次持久化保存。save命令会在执行期间阻塞redis-server进程，直到持久化结束。持久化期间redis不会处理任何命令，也无法向外提供服务。

2.手动bgsave命令

bgsave

执行bgsave命令也会立即执行一次持久化保存。但是bgsave会后台运行save，服务器进程redis-server生成一个子进程，子进程负责完成保存进程，该期间不会造成阻塞。

3.手动条件触发

在配置文件中做相应的设置后，redis会根据设置信息自动调用bgsave命令执行。

**查看上一次的持久化时间是在什么时候**：

lastsave -----返回时间戳

**RDB优化配置**

RDB相关的配置在redis.conf文件的SNAPSHOTTING部分。

【写时复制技术】是linux系统的一种进程管理技术。原本在unix系统中，当一个主进程通过fork()系统调用创建子进程后，内核进程会复制主进程的整个内存空间中的数据，然后分配给子进程。现代的linux则采用了更有效的方式：写时复制。子进程会继承父进程的所有资源，其中就包括主进程的内存空间。即子进程与父进程共享内存。只要内存被共享 ，那么该内存就是只读的（写保护的）。此时，任何一方写入数据到共享内存时都会异常，此时内核进程会将需要写入的数据写入到一个非共享的内存区域。

### AOF持久化

如果出现服务器断电的情况，RDB会出现30s左右的数据丢失，而AOF处理可以减小到最后一秒或最后一次。AOF是指将每一次的写操作都以日志的形式记录到一个AOF文件中的持久化技术。当需要恢复内存数据时，这些写操作重新执行一次，便会恢复到之前的内存数据状态。

开启AOF：appendonly yes

RDB与AOF之间的选择，当aof-use-rdb-preamble禁用的时候，只是为了向下兼容，也就是说当RDB出现异常的时候会走AOF的方式。

### Rewrite机制

redis引入了Rewrite机制来对AOF文件进行压缩，所谓Rewrite其实就是对AOF文件进行重写整理。当rewrite开启后，主进程redis-server创建出一个子进程bgrewriteaof，由该子进程完成rewrite过程。其首先对现有aof文件进行rewrite计算，将计算结果写入到一个临时文件，写入完毕后，再rename该临时文件为原aof文件名，覆盖原有文件。

**rewrite计算**

rewrite计算也称为rewrite策略。rewrite计算遵循以下策略：

》读操作命令不写入文件

》无效命令不写入文件

》过期数据不写入文件

》多条命令合并写入文件

### AOF优化配置

**数据同步**

appendfsync [no always everysec]

no 三十秒刷新一次缓存到磁盘或者到磁盘预置空间的时候

always 每一次写操作都会刷新到磁盘

everysec （默认）每一秒刷新一次数据到磁盘

### RDB和AOF的对比

**RDB 优势与不足**

优势：

文件小，数据恢复快

不足：

安全性比较差，写时复制会降低性能，rdb文件可读性差

**AOF 优势与不足**

优势：

安全性高，丢失量小，可读性高

不足：

AOF文件比较大，写操作会影响性能，数据恢复较慢

### Redis主从集群

