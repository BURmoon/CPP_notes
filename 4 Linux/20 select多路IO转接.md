## 1 select多路IO转接

> 原理：同时监听多个文件描述符, 将监控的操作交给内核去处理

- 数据类型fd_set：文件描述符集合(本质是位图)
- select优缺点
  - 缺点：	
    - 监听上限受文件描述符限制(最大 1024)
    - 提高了编码难度
    - 当客户端多个连接，但少数活跃的情况，select效率较低
  - 一个进程可以支持多个客户端
    - 跨平台(win、linux、macOS、Unix、类Unix、mips)

## 2 文件描述符集合相关函数

- 清空一个文件描述符集合（把文件描述符集合里所有位清0）

  `void FD_ZERO(fd_set *set);`

- 将待监听的描述符添加到集合（把文件描述符集合里fd位置1）

  `void FD_SET(int fd, fd_set *set);`

- 将描述符从监听集合中移除（把文件描述符集合里fd清0）

  `void FD_CLR(int fd, fd_set *set);`

- 判断一个文件描述符是否在监听集合中

  `int FD_ISSET(int fd, fd_set *set);`

## 3 select相关函数

- 委托内核监控该文件描述符对应的读,写或者错误事件的发生

  ```
  int select(int nfds, fd_set *readfds, fd_set *writefds,
  			fd_set *exceptfds, struct timeval *timeout);
  --nfds：监听的所有文件描述符中，最大文件描述符+1
  --readfds：传入、传出参数，读文件描述符监听集合	
  --writefds：传入、传出参数，写文件描述符监听集合
  --exceptfds：传入、传出参数，异常文件描述符监听集合
  --timeout：
  	> 0：设置监听超时时长
  	NULL：阻塞监听
  	0：非阻塞监听，轮询
  函数返回值
  	> 0：所有监听集合（3个）中，满足对应事件的总数。
  	0：timeout时间到（超时），没有检测到读事件
  	-1：错误，设置errno
  ```

  - 若返回值 <0 && errno == EINTR 说明select的过程中被别的信号中断

## 4 select代码思路

```
lfd = socket() ;		//创建套接字
int maxfd = lfd；
bind();					//绑定地址结构
listen();				//设置监听上限
fd_set rset，allset;		//创建 rset, allset 监听集合
FD_ZERO(&allset);		//将 allset 监听集合清空
FD_SET(lfd, &allset);	//将 lfd 添加至 allset 集合中。
while(1) {
	rset = allset；		//保存监听集合
	// 监听文件描述符集合对应事件
	ret = select(maxfd+1，&rset，NULL，NULL，NULL);	
    // 有监听的描述符满足对应事件
	if(ret > 0) {		
		// lfd在rset监听集合中
		if (FD_ISSET(lfd, &rset)) {	
			cfd = accept()；			//建立连接，返回用于通信的文件描述符
			maxfd = cfd；
			FD_SET(cfd, &allset);	//添加到监听通信描述符集合中
			// 修改内核监控的文件描述符的范围
  			if(maxfd < cfd)
  				maxfd = cfd;
 			if(--ret == 0)
  				continue;
		}
		// 有客户端数据发来
		for （i = lfd+1； i <= maxfd; i++）{
			FD_ISSET(i, &rset)	//有read事件
			//进行项目处理
		}	
	}
}
```

