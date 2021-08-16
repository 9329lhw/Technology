## redis
### redis常见数据类型
    1.string--->计数，session共享
    2.lis--->
    3.hash--->用于存储对象
    4.set--->用户标签
    5.sort set--->用于排序
    6.geo
    7.storm
    8.bitmap
### redis list,hash,使用比较慢的命令为什么慢
### redis为什么那么快
    
    1.完全基于内存
    2.数据结构简单
    3.采用单线程避免
    4.使用多路io复用模型，非阻塞io
    5.使用底层epoll
#### redis为什么是单线程  
    
    redis的瓶颈最有可能是机器内存或者网络带宽，单线程容易实现且cpu
    不会成为瓶颈那就顺理成章的采用单线程方案
### 主从复制
    全量复制
    1.Redis 内部会发出一个同步命令，刚开始是 Psync 命令，Psync ? -1表示要求 master 主机同步数据
    2.机会向从机发送 runid 和 offset，因为 slave 并没有对应的 offset，所以是全量复制
    3.从机 slave 会保存 主机master 的基本信息 save masterInfo
    4.主节点收到全量复制的命令后，执行bgsave（异步执行），在后台生成RDB文件（快照），并使用一个
    缓冲区（称为复制缓冲区）记录从现在开始执行的所有写命令
    5.主机send RDB 发送 RDB 文件给从机
    6.发送缓冲区数据
    7.刷新旧的数据，从节点在载入主节点的数据之前要先将老数据清除
    8.加载 RDB 文件将数据库状态更新至主节点执行bgsave时的数据库状态和缓冲区数据的加载。
![RUNOOB 图标](asset/redismslave.png)

    部分复制
    1.如果网络抖动（连接断开 connection lost）
    2.主机master 还是会写 replbackbuffer（复制缓冲区）
    3.从机slave 会继续尝试连接主机
    4.从机slave 会把自己当前 runid 和偏移量传输给主机 master，并且执行 pysnc 命令同步
    5.如果 master 发现你的偏移量是在缓冲区的范围内，就会返回 continue 命令
    6.同步了 offset 的部分数据，所以部分复制的基础就是偏移量 offset。
![RUNOOB 图标](asset/redismslave_2.png) 
### 持久化
#### Rdb 
    优点：
    1，压缩后的二进制文，适用于备份、全量复制，用于灾难恢复 
    2，加载 RDB 恢复数据远快于 AOF 方式 
    缺点：
    1，无法做到实时持久化，每次都要创建子进程，频繁操作成本过高 
    2，保存后的二进制文件，存在老版本不兼容新版本 rdb 文件的问题 
#### Aof: 
    1，所有的写入命令(set hset)会 append 追加到 aof_buf 缓冲区中 
    2，AOF 缓冲区向硬盘做 sync 同步 
    3，随着 AOF 文件越来越大，需定期对 AOF 文件 rewrite 重写，达到压缩 
    4，当 redis 服务重启，可 load 加载 AOF 文件进行恢复 
#### Rdb 和 aof 的加载顺序： 
    1，当 AOF 和 RDB 文件同时存在时，优先加载 AOF 
    2，若关闭了 AOF，加载 RDB 文件 
    3，加载 AOF/RDB 成功，redis 重启成功 
    4，AOF/RDB 存在错误，启动失败打印错误信息
    RDB手可以手动触发和自动触发：
    ·手动触发：save 和 bgsave ，bgsave 是主流的触发 RDB 持久化方式
    ·自动触发：
        # 900s内至少达到一条写命令 save 900 1 
        # 300s内至少达至10条写命令 save 300 10 
        # 60s内至少达到10000条写命令 save 60 10000
    AOF 重写过程可以手动触发和自动触发：
    ·手动触发：直接调用 bgrewriteaof 命令。 
    ·自动触发：根据 auto-aof-rewrite-min-size和auto-aof-rewrite-percentage 参数确定自动触发时机。
### 缓存淘汰机制
        1.FIFO：First In First Out，先进先出。判断被存储的时间，离目前最远的数据优先被淘汰。
        2.LRU：Least Recently Used，最近最少使用。判断最近被使用的时间，目前最远的数据优先被 淘汰。
        3.LFU：Least Frequently Used，最不经常使用。在一段时间内，数据被使用次数最少的，优先 被淘汰。
    1. noeviction：拒绝写请求，正常提供读请求，这样可以保证已有数据不会 丢失（默认策略）；
    2. volatile-lru：尝试淘汰设置了过期时间的 key，虽少使用的 key 被淘汰， 没有设置过期时间的 key 不会淘汰； 
    3. volatile-ttl：跟 volatile-lru 几乎一样，但是他是使用的 key 的 ttl 值进行比较，最先淘汰 ttl 最小的 key； 
    4. volatile-random：其他同上，唯一就是他通过很随意的方式随机选择淘汰 key 集合中的 key； 
    5. allkeys-lru：区别于 volatile-lru 的地方就是淘汰目标是全部 key，没 设置过期时间的 key 也不能幸免； 
    6. allkeys-random：这种方式同上，随机的淘汰所有的 key。
### 内存优化
    一致性 hash 算法（CRC16 校验算法）
### 哨兵原理
    1.检测问题，主要讲的是三个定时任务，这三个内部的执行任务可以保证出现问题马上让 Sentinel 知道。 
    2.发现问题，主要讲的是主观下线和客观下线。当有一台 Sentinel 机器发现问题时，它就会主观对它主观下线。 
    但是当多个 Sentinel 都发现有问题的时候，才会出现客观下线。 
    3.找到解决问题的人，主要讲的是领导者选举，如何在 Sentinel 内部多台节点做领导者选举，选出一个领导者。 
    4.解决问题，主要讲的是故障转移，即如何进行故障转移
        1. 三个定时任务
        每10秒每个 Sentinel 对 Master 和 Slave 执行一次 Info Replication 。 
        每2秒每个 Sentinel 通过 Master 节点的 channel 交换信息（pub/sub）。 
        每1秒每个 Sentinel 对其他 Sentinel 和 Redis 执行 pin 
        第一个定时任务，指的是 Redis Sentinel 可以对 Redis 节点做失败判断和故障转移，在 Redis 内部有
        三个定时任务作为基础，来 Info Replication 发现 Slave 节点， 这个命令可以确定主从关系。 
        第二个定时任务，类似于发布订阅， Sentinel 会对主从关系进行判定，通过 sentinel:hello 频道交互。了解
        主从关系可以帮助更好的自动化操作 Redis 。然后 Sentinel 会告知系统消息给其它 Sentinel 节点，最终达到共识，
        同时 Sentinel节点能够互相感知到对方。 
        第三个定时任务，指的是对每个节点和其它 Sentinel 进行心跳检测，它是失败判定的依据
### 集群
#### 集群的几种方式（https://blog.csdn.net/drdongshiye/article/details/84204392）
	一、单节点实例
	二、主从模式（master/slaver）
	三、sentinel模式
	四、cluster模式
### 常见问题
    1.缓存穿透：查询一个数据库一定不存在的数据
    解决：
    1)增加用户鉴权校验
    2）布隆过滤器
    3）设置空值缓存对象
    2.缓存击穿：对于一些设置了过期时间key值如果这些值可能会在某些时间
    点被超高并发的访问，是一种非常“热点”的数据
    解决：
    1）设置热点数据用不过期
    2）加互斥锁
    3）提前加互斥锁，在读取数据时重新更新缓存
    3.缓存雪崩：缓存大量失效，导致大量请求都直接向数据库获取数据，造成数据库
    的压力
    解决；
    1）加锁降低数据库压力
    2）设置redis过期时间上时加一个随机数避免大批数据过期
    3）部署分布式redis，在一台redis服务器故障时，立刻将请求转移到另一台服务器
    4.缓存与数据库双写一致性
    1）先删除缓存，再修改数据库
    2）
