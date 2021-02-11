## 1 Socket概述

- Socket

  - 在Linux环境下，用于表示进程间网络通信的特殊文件类型
  - 本质为内核借助缓冲区形成的伪文件

- 当调用socket函数以后, 返回一个文件描述符, 内核会提供与该文件描述符相对应的读和写缓冲区

  同时还有两个队列, 分别是请求连接队列和已连接队列

- 在TCP/IP协议中，“IP地址+TCP或UDP端口号”唯一标识网络通讯中的一个进程

  “IP地址+端口号”就对应一个socket

## 2 Socket相关函数

> #include <sys/types.h>
>
> #include <sys/socket.h>
>
> #include <arpa/inet.h>

- 创建一个套接字

  > socket()打开一个网络通讯端口，如果成功的话，返回一个文件描述符
  >
  > ```
  > int socket(int domain, int type, int protocol);
  > --domain:
  > 	AF_INET		使用TCP或UDP来传输，用IPv4的地址
  > 	AF_INET6	使用TCP或UDP来传输，用IPv6的地址
  > 	AF_UNIX		本地协议，一般都是当客户端和服务器在同一台及其上的时候使用
  > --type:
  > 	SOCK_STREAM	这个socket是使用TCP来进行传输
  > 	SOCK_DGRAM	这个socket是使用UDP来进行连接
  > --protocol:
  > 	传0，表示使用默认协议
  > 函数返回值：
  > 	成功：返回指向新创建的socket的文件描述符
  > 	失败：返回-1，设置errno
  > ```

- 绑定网络地址和端口号

  > #include <arpa/inet.h>
  >
  > bind()的作用是将参数sockfd和addr绑定在一起
  >
  > ```
  > int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);		
  > --sockfd：socket文件描述符
  > --addr：构造出IP地址加端口号
  > --addrlen：addr长度，sizeof(addr) 
  > 函数返回值
  > 	成功：0
  > 	失败：-1, 设置errno
  > ```

  - 服务器需要调用bind绑定一个固定的网络地址和端口号
  - 使sockfd这个用于网络通讯的文件描述符监听addr所描述的地址和端口号

- 设置监听

  > listen()声明sockfd处于监听状态
  >
  > ```
  > int listen(int sockfd, int backlog);
  > --sockfd：socket文件描述符
  > --backlog：排队建立3次握手队列和刚刚建立3次握手队列的链接数和
  > 函数返回值
  > 	成功：0
  > 	失败：-1, 设置errno
  > ```

  - 最多允许有backlog个客户端处于连接待状态，如果接收到更多的连接请求就忽略

    (设置同时与服务器建立连接的上限数)

- 等待连接

  > 阻塞等待客户端建立连接，成功的话，返回一个与客户端成功连接的socket文件描述符
  >
  > ```
  > int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
  > --sockdf：socket文件描述符
  > --addr：传出参数，返回链接客户端地址信息，含IP地址和端口号
  > --addrlen：传入传出参数（值-结果）
  > 	传入sizeof(addr)大小，函数返回时返回真正接收到地址结构体的大小
  > 函数返回值：
  > 	成功：一个新的socket文件描述符，用于和客户端通信
  > 	失败：-1, 设置errno
  > ```

  - accept()是从已连接队列中获取一个新的连接，并获得一个新的文件描述符，该文件描述符用于和客户端通信

- 建立连接

  > 使用现有的socket文件描述符与服务器建立连接
  >
  > ```
  > int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
  > --sockdf：socket文件描述符
  > --addr：传入参数，指定服务器端地址信息，含IP地址和端口号
  > --addrlen：传入参数,传入sizeof(addr)大小
  > 函数返回值
  > 	成功：0
  > 	失败：-1, 设置errno
  > ```

  - 客户端需要调用connect()连接服务器，connect和bind的参数形式一致

    区别在于bind的参数是自己的地址，而connect的参数是对方的地址

