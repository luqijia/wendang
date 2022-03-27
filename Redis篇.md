#### 1、Redis有哪些好处

（1） 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)

（2）支持丰富数据类型，支持String，list，set，sorted set，hash

（3）支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行。

（4）丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除。

#### 2、Redis相比Memcached有哪些优势

（1）Memcached所有的值均是简单的字符串，Redis则有更丰富的数据类型。

（2）redis的速度比memcached快得多。

（3）redis提供aof和rdb两种数据持久化策略。

（4）Redis支持数据的备份，即master-slave模式的数据备份.

（5）使用底层模型不同，它们之间底层实现方式 以及与客户端之间通信的应用协议不一样（redis使用的是resp协议，可以把数据类型进行序列化，简单字符串是“+”，错误是“-”，整数是“：”，批量字符串是“$”，数组是“*”，协议的不同部分始终以“\ r \ n”（CRLF）结束）。Redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。

（6）value大小：redis最大可以达到1GB，而memcache只有1MB

#### 3、**Redis常见性能问题和解决方案：**

（1） Master最好不要做任何持久化工作，如RDB内存快照和AOF日志文件；(Master写内存快照，save命令调度rdbSave函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以Master最好不要写内存快照;AOF文件过大会影响Master重启的恢复速度)

（2）如果数据比较重要，某个Slave开启AOF备份数据，策略设置为每秒同步一次

（3）为了主从复制的速度和连接的稳定性，Master和Slave最好在同一个局域网内

（4）尽量避免在压力很大的主库上增加从库

（5）主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1 <- Slave2 <- Slave3...；这样的结构方便解决单点故障问题，实现Slave对Master的替换。如果Master挂了，可以立刻启用Slave1做Master，其他不变

#### 4、Redis回收策略

（1）volatile-lru：从设置过期时间的key中（server.db[i].expires）根据最近最少使用原则回收数据

（2）volatile-ttl：从设置过期时间的key中（server.db[i].expires）挑选将要过期的数据淘汰

（3）volatile-random：从设置过期时间的key中（server.db[i].expires）任意选择数据淘汰

（4）allkeys-lru：从所有key中（server.db[i].dict）根据最近最少使用原则回收数据

（5）allkeys-random：从所有key中（server.db[i].dict）任意选择数据淘汰

（6）no-enviction：禁止驱逐数据

> 注意这里的6种机制，volatile和allkeys规定了是对已设置过期时间的数据集淘汰数据还是从全部数据集淘汰数据，后面的lru、ttl以及random是三种不同的淘汰策略，再加上一种no-enviction永不回收的策略。

使用策略原则：

1、如果数据呈现幂律分布，也就是一部分数据访问频率高，一部分数据访问频率低，则使用allkeys-lru。

2、如果数据呈现平等分布，也就是所有的数据访问频率都相同，则使用allkeys-random。
