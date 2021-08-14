## nginx
### Nginx与Apache对比
#### 1.1 nginx 
    1. 轻量级，采用 C 进行编写，同样的 web 服务，会占用更少的内存及资源 
    2. 抗并发，nginx 以 epoll and kqueue 作为开发模型，处理请求是异步非阻塞的，负载能力比 apache 高很多，而 apache 则是阻塞型的。在高并发下 nginx 能 保持低资源低消耗高性能 ，而 apache 在 PHP 处理慢或者前端压力很大的情况下，很容易出现进程数飙升，从而拒绝服务的现象。 
    3. nginx 处理静态文件好，静态处理性能比 apache 高三倍以上 
    4. nginx 的设计高度模块化，编写模块相对简单 
    5. nginx 配置简洁，正则配置让很多事情变得简单，而且改完配置能使用 -t 测试配置有没有问题，apache 配置复杂 ，重启的时候发现配置出错了，会很崩溃 
    6. nginx 作为负载均衡服务器，支持 7 层负载均衡 
    7. nginx 本身就是一个反向代理服务器，而且可以作为非常优秀的邮件代理服务器 
    8. 启动特别容易, 并且几乎可以做到 7*24 不间断运行，即使运行数个月也不需要重新启动，还能够不间断服务的情况下进行软件版本的升级 
    9. 社区活跃，各种高性能模块出品迅速 
#### 1.2 apache 
    1. apache 的 rewrite 比 nginx 强大，在 rewrite 频繁的情况下，用 apache 
    2. apache 发展到现在，模块超多，基本想到的都可以找到 
    3. apache 更为成熟，少 bug ，nginx 的 bug 相对较多 
    4. apache 超稳定 
    5. apache 对 PHP 支持比较简单，nginx 需要配合其他后端用 
    6. apache 在处理动态请求有优势，nginx 在这方面是鸡肋，一般动态请求要 apache 去做，nginx 适合静态和反向。 
    7. apache 仍然是目前的主流，拥有丰富的特性，成熟的技术和开发社区
### 限流模块
#### ngx_http_limit_conn_module限制连接数
        语法: limit_req zone=name [burst=number] [nodelay]; 
        语法: limit_req_zone $variable zone=name:size rate=rate; 
        $variable：变量 
        zone：代表当前限制的名称与存放大小 
        name：名称 
        size：存放客户端信息的大小 
        rate：表示速率 
        默认值: none 
        上下文: http
#### ngx_http_limit_reg_module限制请求频率
        语法: limit_conn_zone $binary_remote_addr zone=addr:10m; 
        默认值: none 
        配置段: http 
        例子：limit_conn_zone $binary_remote_addr zone=addr:10m; 
        说明：区域名称为addr，大小为10m，键值是客户端IP。 
        如果限制域的存储空间耗尽了，对于后续所有请求，服务器都会返回 503 (Service Temporarily Unavailable)错误。
#### 限制算法
##### 令牌桶
    令牌以固定速率产生，并缓存到令牌桶中； 
    令牌桶放满时，多余的令牌被丢弃；
    请求要消耗等比例的令牌才能被处理； 
    令牌不够时，请求被缓存。
##### 漏桶
    水（请求）从上方倒入水桶，从水桶下方流出（被处理）； 来
    不及流出的水存在水桶中（缓冲），以固定速率流出； 
    水桶满后水溢出（丢弃）。 
    这个算法的核心是：缓存请求、匀速处理、多余的请求直接丢弃。 
    相比漏桶算法，令牌桶算法不同之处在于它不但有一只“桶”，还有个队列，这个桶是用来存放令牌的，队列才是用来存放请求的
### IP访问控制模块
    allow
    deny 
### ip黑白名单
#### ngx_http_geo_module
#### ngx_http_map_module    

### rewrite模块
### 防盗链

### 缓存机制

### 代理
    Proxy 模块
#### 反向代理
#### 正向代理
### 半自动平滑升级
### 负载均衡
#### 1、轮询
#### 2、加权轮询
#### 3、IP Hash
#### 4、最少连接数
### 动态负载均衡
    nginx-upsync-module 提供了动态的负载均衡
### nginx优化配置
#### SYN攻击
    攻击者短时间伪造不同IP地址的SYN报文，快速占满backlog队列，
    使服务器不能为正常用户服务，SYN攻击是所有黑客攻击事件中最常见又
    最容易被利用的一 种攻击手法，由来已久，破坏威力巨大。SYN攻击属
    于DOS攻击的一种， 它利用TCP协议缺陷，通过发送大量的半连接请求，
    耗费CPU和内存资源
#### 如何应对SYN攻击？
    SYN攻击的原理就是向服务器发送SYN数据包，并伪造源IP地址。服务器在收到SYN数据包时，
    会将连接加入backlog队列，并向源IP发送SYN-ACK数据包，并 等待ACK数据包，
    以完成三次握手建立连接。 由于源IP地址是伪造的不存在主机IP，所以服务器无法
    收到ACK数据包，并会不断重发，同时backlog队列被不断被 攻击的SYN连接占满，
    导致无法处理正常的连接小号cpu资源。
    1、减少SYN-ACK数据包的重发次数（默认是5次） 
        sysctl -w net.ipv4.tcp_synack_retries=3 
        sysctl -w net.ipv4.tcp_syn_retries=3
    2、增大backlog队列（默认是1024） 
    • net.core.netdev_max_backlog -接收自网卡、
        但未被内核协议栈处理的报文队列长度 
    • net.ipv4.tcp_max_syn_backlog -SYN_RCVD状态连接的最大个数
    sysctl -w net.ipv4.tcp_max_syn_backlog=2048
    3、超出处理能力时，对新来的SYN丢弃连接
    4、生成验证cookie，重连
### Gzip