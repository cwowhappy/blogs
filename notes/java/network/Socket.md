### 网络编程之Socket
* `Socket`:`Socket`实例表示TCP协议的客户端
* `ServerSocket`:`ServerSocket`实例表示TCP协议的服务端

#### 1.TCP/IP协议族：
* TCP协议: 传输控制协议（Transmission Controll Protocol），是一种面向连接的、可靠的、基于字节流的传输层通信协议；端口到端口的通信
* UDP协议: 用户数据包协议（User Datagram Protocol），是一种无连接的传输层通信协议
* IP协议: 网络互联协议（Internet Protocol）；计算机之间的通信（IP地址到IP地址的通信）

#### 2.`Socket`与`ServerSocket`
每个`Socket`实例关联一个`InputStream`实例和一个`OutputStream`实例

疑问点：

1.`ServerSocket`的`accept()`方法返回的`Socket`实例与客户端的通信，是不是与`ServerSocket`的监听端口共用一个？是

2.`ServerSocket`的监听端口接受到的信息是如何分发到每个`Socket`实例的？


#### 参考博文
[1]. http://wiki.jikexueyuan.com/project/java-socket/tcp.html
[2]. http://www.jianshu.com/p/ef892323e68f