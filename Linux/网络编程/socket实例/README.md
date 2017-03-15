Socket
====

socket是用户进程与内核网络协议栈的编程接口。   

* 应用程序调用某个socket函数时TCP协议层完成什么动作，比如调用connect()会发出SYN段
* 应用程序如何知道TCP协议层的状态变化，比如从某个阻塞的socket函数返回就表明TCP协议收到了某些段，再比如read()返回0就表明收到了FIN段

服务器端应用层：   
socket(),bind(listenfd,服务器地址端口),listen(listenfd,连接队列长度),accept(listenfd,客户端地址端口)    
客户端应用层：  
socket(),connect(fd,服务器地址端口),connect返回    

###基本socket函数<sys/socket.h>
* 创建一个套接字用于通信
```
int socket(int domain, int type, int protocol)
```
参数   
domain ：指定通信协议族（protocol family），AF_INET、AF_INET6、AF_UNIX等      
type：指定socket类型，流式套接字SOCK_STREAM，数据报套接字SOCK_DGRAM，原始套接字SOCK_RAW      
protocol ：协议类型，IPPROTO_TCP等；一般由前两个参数就决定了协议类型，设置为0即可。     
返回值：成功返回非负整数， 它与文件描述符类似，我们把它称为套接口描述字，简称套接字。失败返回-1   
*  绑定一个本地地址到套接字
```
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen)

```
参数   
sockfd：socket函数返回的套接字     
addr：要绑定的地址   
addrlen：地址长度   
返回值：成功返回0，失败返回-1     
* 将套接字用于监听进入的连接   
```
int listen(int sockfd, int backlog)
```
sockfd：socket函数返回的套接字   
backlog：已完成三次握手的最大连接个数     
返回值：成功返回0，失败返回-1     
* 从已完成连接队列返回第一个连接，如果已完成连接队列为空，则阻塞   
```
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen)
```
参数   
sockfd：服务器套接字    
addr：将返回对等方的套接字地址    
addrlen：返回对等方的套接字地址长度    
返回值：成功返回非负整数，失败返回-1     
* 建立一个连接至addr所指定的套接字
```
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen)
```
参数   
sockfd：未连接套接字   
addr：要连接的套接字地址   
addrlen：第二个参数addr长度   
返回值：成功返回0，失败返回-1   
###fork
网络服务器通常用fork来同时服务多个客户端，父进程专门负责监听端口，每次accept一个新的客户端链接就fork出一个子进程专门
服务这个客户端。但是子进程退出时会产生僵尸进程，父进程要注意处理SIGCHLD信号和调用wait清理僵尸进程，最简单的办法就是直接忽略SIGCHLD信号。

