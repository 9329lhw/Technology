## 网络安全
### xss跨站脚本攻击
通过表单注入js代码，当数据被执行的时候，跨域获取网站的数据

防范：使用htmlspecialchars()将特殊字符转换为 HTML 实体
### csrf跨站请求伪造
通过模拟表单上传用户信息 伪造用户 进行恶意提交

防范：生成表单的时候给表单一个token，后台接受到数据的时候验证token
### 点击劫持
攻击者使用一个或多个透明的 iframe 覆盖在一个正常的网页上，然后诱使用户在该网页上进行操作，当用户在不知情的情况下
击透明的 iframe 页面时，用户的操作已经被劫持到攻击者事先设计好的恶意按钮或链接上

劫持漏洞的防御：

    1.服务端：
        1、 X-FRAME-OPTIONS 机制
        2、 使用 FrameBusting 代码
        3、 使用认证码认证用户
        
    2.客户端
        1、 升级浏览器
        2、 NoScript 扩展
### 传输过程安全问题
        1.http窃听
        2.http篡改
        3.HTTP传输窃听，篡改解决方法
            https是对http协议的一种解决方案
            http被窃听和篡改的原因，就是http是明文传输的。所以可以对数据进行加密处理
           我们对HTTP进行加密，加密的方式就是TLS协议（传输层加密），TLS在之前的叫法就是SSL
        4.中间人攻击
            浏览器对数据进行加密，通过中间人与服务器进行通信。
            中间人可以解密浏览器的信息，然后加密后发送给服务器。
            同时中间人也可以解密服务器的信息，然后加密后发送给浏览器。
            这样也会受到http的窃听和篡改风险           
        5.中间人攻击解决方法:
            CA证书机制
### 密码攻击
        
### sql注入
        预防方法：服务端使用php函数过滤掉一些特殊符号就行
### 文件上传
        上传问题防御:
            限制上传后缀
            文件类型检查：通过mime判断文件类型，不是完全可靠的，是通过浏览器提供的，攻击者可以绕
            过浏览器发起请求，伪造mime
            文件内容检查：文件内容的二进制数据前几位是有一定特征的，根据这个特征可以判断文件类型，
            比根据mime更可靠
            程序输出：程序读取源文件，直接输出，不执行，比上面的可靠
            权限控制原则-可写可执行互斥：设置文件权限，上传的文件目录可写，但是不可执行
### dos攻击：分布式拒绝服务(DDoS:Distributed Denial of Service)
        该攻击方式利用目标系统网络服务功能缺陷或者直接消耗其系统资源，
        使得该目标系统无法提供正常的服务。攻击者进行拒绝服务攻击，实际上让服务器实现两种效果：
        一是迫使服务器的缓冲区满，不接收新的请求；
        二是使用IP欺骗，迫使服务器把合法用户的连接复位，影响合法用户的连接。
### cc攻击(Challenge Collapsar)
        CC攻击的原理是通过代理服务器或者大量肉鸡模拟多个用户访问目标网站的动态页面，制造大量的后台
        数据库查询动作，消耗目标CPU资源，造成拒绝服务。
    DDoS攻击与CC攻击的区别
        DDoS攻击打的是网站的服务器，而CC攻击是针对网站的页面攻击的，用术语来说就是，一个是WEB网络
        层拒绝服务攻击（DDoS），
        一个是WEB应用层拒绝服务攻击（CC）。CC攻击模拟用户对一些比较消耗资源的网页进行攻击，而DDoS
        攻击则是针对ip进行攻击，
        两者的危害也是不一样的，DDoS的攻击会比CC攻击更难防御，造的危害会更大
### 重放攻击
### ARP欺骗
### IP欺骗
### SYN攻击：
    在三次握手过程中，Server发送SYN-ACK之后，收到Client的ACK之前的TCP连接称为半连接
    （half-open connect），此时Server处于SYN_RCVD状态，当收到ACK后，Server转入
    ESTABLISHED状态。SYN攻击就是Client在短时间内伪造大量不存在的IP地址，并向Server
    不断地发送SYN包，Server回复确认包，并等待Client的确认，由于源地址是不存在的，
    因此，Server需要不断重发直至超时，这些伪造的SYN包将产时间占用未连接队列，导致
    正常的SYN请求因为队列满而被丢弃，从而引起网络堵塞甚至系统瘫痪。
    SYN攻击时一种典型的DDOS攻击，检测SYN攻击的方式非常简单，即当Server上有大量半连接
    状态且源IP地址是随机的，则可以断定遭到SYN攻击了，使用如下命令可以让之现行：
    #netstat -nap | grep SYN_RECV  
    
参考地址：

1.https://serverless-action.com/fontend/web-security/

2.https://www.huaweicloud.com/zhishi/dyl86.html