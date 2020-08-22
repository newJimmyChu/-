# Java 面试笔记

## 第二章 网络

### 2.1 OSI七层协议

第一层：物理层： 传输比特流，转换比特流为强弱电流

第二次：数据链路层： 错误检测，定义如何格式化数据，控制对物理层的访问 （交换机）对帧解码

第三层：网络层： 将网络地址翻译为物理地址，和路由，控制子网的运行（路由器）（数据包）IP协议 分组传输

第四层：传输层： 接受上一层数据，进行数据分割，流量控制，排序  TCP协议，UDP协议

第五层：会话层： 建立管理应用程序的通信

第六层：表示层： 格式化

第七层： 应用层： 消息头记录消息体信息，以便接收数据 HTTP协议

每层会叠加数据头 自上而下增加；解码时自下而上解码

#### 2.1.1 OSI实现：TCP/IP

四个概念层模型：应用层->传输层->网络层->链路层

自上而下添加头部：应用层（HTTP）->传输层（添加TCP首部）->网络层（添加IP首部）->链路层（添加以太网首部）

解码时自下而上解析每层头部



### 2.2 TCP三次握手

可靠的基于字节流的传输层通信协议

切割应用层数据流->报文段发送给目标节点的TCP层

数据包拥有seq，对方收到回复ACK确认，未收到则重传

使用校验和确认包的完整性

字节占用：

1. src port 和 dest port分别占用2字节
2. seq num 4字节
3. ack num 4字节
4. offset，reserved 各1字节，tcp flag 2字节，window 2字节
5. checksum，urgent pointer 各两字节
6. TCP options 4字节

TCP 端口号可以唯一标识一个进程

TCP flags：

1. URG 紧急指针表示
2. ACK 确认序号标志
3. PSH push标志
4. RST reset重置连接
5. SYN 同步序号，用于建立连接过程
6. FIN finish标志，用于释放连接

#### 三次握手

建立初始化连接的三次握手：

1. 客户端发送 SYN=1, seq=x， 进入SYN_SEND状态
2. 服务器确认客户SYN包，ack=x+1，发送 SYN=1, ACK=1, seq=y, ack=x+1， 进入SYN_RECV状态
3. 客户端确认服务器SYN+ACK包，ack=y+1， 发送端发送 SYN=1, ACK=1, seq=x+1, ack=y+1， 进入ESTABLISHED状态

注明：ack为接收数据的位置，seq为发送数据的位置

**为什么需要三次握手：**初始化 Sequence Number 的值

**首次握手隐患：**SYN超时问题： 

1. Server收到Client的SYN，回复SYN-ACK时未收到ACK确认
2. Server重复尝试五次，默认等待63秒断开连接 1->2->4->8->16

防范SYN Flood攻击：

1. SYN 队列满后，通过tcp_syncookies参数回发SYN Cookie
2. 若为正常连接则Clinet会回发SYN Cookie，建立连接



### 2.3 TCP 四次挥手

**挥手：**终止连接

四次挥手流程：

1. 客户端发送终止报文 FIN， seq=u，进入FIN-WAIT-1状态
2. 服务器接收后发送 ACK，seq=w，ack=u+1，进入CLOSE-WAIT状态，客户端接收ACK后进入FIN-WAIT-2状态
3. 服务器在返回终止报文后依然可以发送v字节数据给客户端
4. 服务器在CLOSE-WAIT结束后回发终止报文 FIN & ACK，seq=u+1，ack=w+v，进入LAST-ACK状态等待接收最后ACK报文
5. 客户端发送 ACK 给服务器并进入TIME-WAIT状态，服务器进入CLOSE状态
6. 客户端等待 2*MSL（30s） 时间后进入CLOSE状态

为什么TIME_WAIT：

1. 保证足够时间收到ACK包
2. 避免新旧连接混淆

为何要四次挥手才能关闭连接：

1. 发送方和接收方都需要FIN和ACK报文（各只需要2次挥手）

大量CLOSE_WAIT原因：客户端一直在请求，但是一直没收到 FIN 报文，或者服务器没有接收到 ACK 报文

CLOSE_WAIT 过多会导致过多文件句柄被占用，并造成 too many open files 异常



### 2.4 UDP

UDP报文结构：

1. src port   2字节
2. dest port   2字节
3. length   2字节
4. checksum   2字节

UDP特点：

1. 面向非连接
2. 不维护连接状态，支持同时向多个client传输相同消息
3. 报文头只有8字节
4. 吞吐量只限于数据生成和传输速度
5. 不保证可靠性，不需要维持复杂的链接状态表
6. 面向报文，不对应用程序提交报文信息进行拆分或合并

TCO UDP对比：

1. TCP面向链接vs无连接
2. 可靠vs不可靠
3. 有序vs无序
4. 速度慢vs速度快（游戏，广播，视频）
5. 量级大vs小

### 2.5 TCP滑动窗口

RTT: 发送数据包到ACK的时间

RTO: 重传间隔

TCP使用sliding window做流量控制和乱序重排：

1. 保证可靠性
2. 保证流控特性

计算过程：

AdvertisedWindow = MaxRcvBuffer - (LastByteRcvd - LastByteRead)

EffectedWindow = AdWindow - (LastSent - LastAcked)

发送方四类缓存：

1. Sent & ACK
2. Sent & not ACK
3. not Sent but Ready to Receive
4. not Sent not Ready to Receive

接收方四种：

1. Received & ACK
2. not Received Permitted to Send
3. not Received may not send

### 2.6 HTTP 

特点：

1. 支持client / server模式
2. 简单快速 只需请求方法和路径
3. 灵活  允许传输任意类型的数据对象
4. 无连接  每次连接只处理一个请求
5. 无状态协议  对事务处理没有记忆能力，无法处理先前信息

结构： 请求方法 空格 URL 空格 协议版本 回车符 换行符

HTTP相应步骤：

1. client连接到web服务器
2. 发送http请求
3. 接收并返回http响应
4. 释放tcp连接 （keep-alive：保持连接）
5. 客户端解析HTML

浏览器输入URL后->DNS解析->TCP建立连接->发送HTTP请求->服务器处理并返回HTTP->浏览器渲染HTML->close connection

HTTP状态码：

1. 1xx：表示请求已接收，继续处理
2. 2xx：成功，被成功接收
3. 3xx：重定向
4. 4xx：客户端错误
5. 5xx：服务端错误

常见状态码：

1. 200 OK
2. 400 Bad Request：客户端错误
3. 401 Unauthorized： 请求没有权限
4. 403 Forbidden：服务器收到请求但是拒绝服务
5. 404 Not Found：资源部存在
6. 500 Internal Server Error：不可以预期的服务器错误
7. 503 Server Unavailable：当前服务器不能处理客户端请求

#### 2.7 GET 和 POST 请求

对比： 

1. GET 将请求信息放在URL中（GET可以在URL中传递参数），POST放在报文体中（明文的）
2. 数据库：GET符合幂等性（对数据库多次操作返还结果一致）和安全性（不修改数据库），POST不符合
3. GET可以被缓存，存储，但是POST不行

Cookie和Session的区别：

Cookie：服务器给客户端发送的特殊信息，以文本形式存放在客户端。客户端再次请求时，会把Cookie回发。服务器接收到后，会解析Cookie生成与客户端相对应的内容

Cookie设置以及发送过程：

1. 发送HTTP
2. Server发送HTTP Response 并设置Cookie
3. 客户端发送HTTP Request 和 Cookie
4. 服务器返还 HTTP Response

Session：服务器端保存客户端状态的机制。解析客户端去请求操作session id，保存状态信息。

Session实现方式：

1. 使用Cookie实现
2. 使用URL回写实现（JsessionID=xxxxx）
3. Tomcat同时使用Cookie和URL实现Session

Cookie，Session区别：

1. Cookie存在client，Session放在Server上
2. Session更加安全
3. 考虑服务器负担，应当使用Cookie

### 2.8 HTTPS 和 HTTP的区别

HTTPS加入了SSL或者TLS层，增加了安全层

SSL（安全套接层）：采取身份验证和数据加密保证数据和网络安全

加密的方式：

1. 对称加密：加密解密使用相同密钥（性能快）
2. 非对称加密：加密使用密钥不同（公钥私钥）（性能慢，加密量有限，安全性强）
3. 哈希算法：例子：MD5算法，不可逆
4. 数字签名：证明信息是又某人发出的

HTTPS：签名+其他方式

数据传输流程：

1. 浏览器发送给Server加密算法信息
2. Server根据加密算法，以证书形式发回浏览器
3. 浏览器验证证书合法性，结合证书公钥信息加密发给浏览器
4. 服务器使用私钥解密信息，验证哈希，加密响应消息回发浏览器
5. 浏览器解密消息，如果一致，则握手以后将进行加密交互数据

区别：

1. HTTPS需要到CA申请证书，需要交费
2. HTTPS密文传输（SSL加密层），HTTP明文传输
3. HTTPS默认443，HTTP默认80
4. HTTPS=HTTP+加密+认证+完整性保护，比较安全

HSTS（HTTP优化）

### 2.9 Socket

Socket是对TCP/IP协议的抽象，是对操作系统对外开放的接口

Socket通信流程：

1. client: socket->connect->send->close
2. server: socket->bind->listen->accept->recv->close

UDP in Java: DatagramSocket() & DatagramPacket()

