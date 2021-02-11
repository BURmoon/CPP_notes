## 1 UDP通信

- TCP：面向连接的，可靠数据包传输

  - 对于不稳定的网络层，采取完全弥补的通信方式，即丢包重传

  - 优点：稳定（数据流量、速度、顺序）

    缺点：传输速度慢，相率低，开销大

  - 使用场景：数据的完整型要求较高，不追求效率（大数据传输、文件传输）

- UDP：无连接的，不可靠的数据报传递

  - 对于不稳定的网络层，采取完全不弥补的通信方式，即默认还原网络状况

  - 优点：传输速度快，效率高，开销小

    缺点：不稳定（数据流量、速度、顺序）

  - 使用场景：对时效性要求较高场合，稳定性其次（游戏、视频会议、视频电话）

    - 腾讯、华为、阿里，通过应用层数据校验协议，弥补udp的不足

## 2 UDP相关函数

- 读入数据recvfrom

  ```
  ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,
  		struct sockaddr *src_addr, socklen_t *addrlen);
  --sockfd：套接字
  --buf：缓冲区地址
  --len：缓冲区大小
  --flags： 0
  --src_addr：传出，对端地址结构
  --addrlen：传入传出
  返回值： 
  	成功：接收数据字节数
  	失败：-1，设置errno
  	0： 对端关闭
  ```

- 写出数据sendto

  ```
  ssize_t sendto(int sockfd, const void *buf, size_t len, int flags,
  		const struct sockaddr *dest_addr, socklen_t addrlen);
  --sockfd：套接字
  --buf：存储数据的缓冲区
  --len：数据长度
  --flags： 0
  --src_addr：传入，目标地址结构
  --addrlen：地址结构长度。
  返回值：	
  	成功：写出数据字节数
  	失败：-1，设置errno	
  ```

- UDP的C/S模型

  ```
  server：
  	lfd = socket(AF_INET, SOCK_DGRAM, 0);	
  	bind();
  	listen();  	//可有可无
  	while（1）{
  		recvfrom();		
  		//数据处理
  		sendto();					
  	}
  	close();
  ```

  ```
  client：
  	connfd = socket(AF_INET, SOCK_DGRAM, 0);
  	sendto（）;
  	recvfrom（）;
  	//数据处理
  	close();
  ```

