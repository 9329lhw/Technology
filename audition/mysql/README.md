# 
    
    事务的日志流程
    1）创建阶段：事务创建一条日志； 
    2）日志刷盘：日志写入到磁盘上的日志文件； （ib_logfile里面） 
    3）数据刷盘：日志对应的脏页数据写入到磁盘上的数据文件； 
    4）写CKP：日志被当作Checkpoint写入日志文件；（ib_data里面）
    
    死锁
    两个事务都持有对方需要的锁，并且在等待对方释放，并且双方都不会释放自己的锁
    	两种解决方式
    	1.等待，直到超时（innodb_lock_wait_timeout=50s）。
    	2.发起死锁检测，主动回滚一条事务，让其他事务继续执行（innodb_deadlock_detect=on）。
    	
    		
    对于事务的建议
    1. 控制事务大小，减少锁定的资源量和锁定时间长度。 
    2. 人所有的数据检索都通过索引来完成，从而避免因为无法通过索引加锁而升级为表锁。 
    3. 减少基于范围的数据检索过滤条件，避免因为间隙锁带来的负面影响而锁定了不该锁定的数据。
    4. 在业务条件允许下，尽量使用较低隔离级别的事务隔离。减少隔离级别带来的附加成本。 
    5. 河里使用索引，让innodb在索引上面加锁的时候更加准确。 
    6. 在应用中尽可能做到访问的顺序执行 
    7. 如果容易死锁，就可以考虑使用表锁来减少死锁的概率
    
    
    mysql基础结构
    客户端=》connection
    服务端=》sql层+存储引擎层
    	sql层=>链接、线程处理+查询缓存+分析器+优化器
    	存储引擎层=》innodb+myisam
    
    connect:其他语言的链接->navicat
    sql层:主要包括权限判断，sql解析功能合查询缓存处理
    	1.链接、线程处理：处理客户端的请求，身份验证和数据库安全性验证
    	2.查询缓存查询分析器是sql层的核心部分，其中主要涉及查询的解析，优化，缓存，以及所有内置的函数，存储过程，出发器，视图等功能
    	3.优化器主要负责存储和获取所有存储在mysql中的数据
    	
    mysql物理文件类型
    	日志文件主要包括：
    		1.错误日志
    		2.二进制日志
    		3.事务日志
    		4.慢查询日志
    		5.查询日志
    	数据库文件：
    		1.'.frm'文件：主要存放与表相关的数据信息，主要包括表结构的定义信息
    		当数据库崩溃时，用户可以通过frm文件来恢复数据表结构
    		2.'.MYD'文件：是myisam存储引擎专用，存放myisam表的数据
    		3.'.MYI'文件：是myisam存储引擎专用，存放myisam表的索引相关信息
    		4.'.ibd','.ibdata'文件：存放innodb数据的文件，共享表空间会用.ibdate文件来存放，独享表空间使用.idb文件来存放
    
    独享表空间与共享表空间的比较
    	共享表空间：
    		有点：可以放表空间分成多个文件存放到各个磁盘上
    		缺点：多表及索引在表空间中混合存储，会产生大量空隙
    	独立表空间
    		优点：易扩展，空间可回收，独立的表空
    		缺点：单表增加过大
    		
    HUP     1    终端断线
    INT     2    中断（同 Ctrl + C）
    QUIT    3    退出（同 Ctrl + ）
    TERM    15   终止（如果kill 命令后直接加进程的pid号 默认选项为-15 ，代表终止此进程）
    KILL    9    强迫终止
    CONT    18   持续（与STOP相反， fg/bg号令）
    STOP    19   暂停（同 Ctrl + Z）