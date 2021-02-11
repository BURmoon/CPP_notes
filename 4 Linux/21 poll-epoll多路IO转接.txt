## 1 poll多路IO转接

> 跟select类似, 委托内核监控可读, 可写, 异常事件

- 优/缺点

  - 优点
    - 自带数组结构，可以将 监听事件集合 和 返回事件集合 分离
    - 拓展 监听上限，超出 1024限制
  - 缺点
    - 不能跨平台
    - 无法直接定位满足监听事件的文件描述符，编码难度较大

- 突破1024文件描述符限制

  - 当前计算机所能打开的最大文件个数，受硬件影响

    `cat /proc/sys/fs/file-max`

  - 当前用户下的进程，默认打开文件描述符个数，缺省为1024

    `ulimit -a`

  - 修改

    - 打开 `sudo vi /etc/security/limits.conf`写入

      设置默认值， 可以直接借助命令修改	`soft nofile 65536`

      命令修改上限	`hard nofile 100000`

## 2 poll相关函数

- poll函数

  ```
  int poll(struct pollfd *fds, nfds_t nfds, int timeout);
  --fds：监听的文件描述符(数组)
  --nfds: 监听数组的实际有效监听个数
  --timeout：
  	>0: 超时时长(毫秒)
  	-1:	阻塞等待
  	0：非阻塞
  函数返回值
  	成功: 返回满足对应监听事件的文件描述符的总个数
  	失败: 返回-1
  	若timeout=0, poll函数不阻塞,且没有事件发生,此时返回-1并且errno=EAGAIN,这种情况不应视为错误
  ```

  - struct pollfd结构体

    ```
    struct pollfd {
    	int fd;			//待监听的文件描述符
    	short events;	//待监听的文件描述符对应的监听事件
    					//取值：POLLIN、POLLOUT、POLLER
    	short revnets;	//传入时， 给0
    					//如果满足对应事件的话，返回POLLIN、POLLOUT、POLLERR
    	}；
    ```

    - 当poll函数返回的时候, 结构体当中的fd和events没有发生变化

      究竟有没有事件发生由revents来判断, 所以poll是请求和返回分离

  - struct pollfd结构体中的fd成员若赋值为-1, 则poll不会监控

- poll服务端模型

  ```
  创建socket, 得到监听文件描述符lfd----socket()
  设置端口复用----setsockopt()
  绑定----bind()
  监听----listen()
  truct pollfd client[1024];
    	client[0].fd = lfd;
    	client[0].events = POLLIN;
  int maxi = 0;
  for(i=1; i<1024; i++){
  	client[i].fd = -1;
  }
  while(1){
  	nready = poll(client, maxi+1, -1);
    	//异常情况
    	if(nready<0){
    		if(errno==EINTR) {	 	// 被信号中断
    			continue;
    		}
    		break;
    	}
    	//有客户端连接请求到来
    	if(client[0].revents==POLLIN){
    		//接受新的客户端连接
    		cfd = accept(lfd, NULL, NULL);
    		//寻找在client数组中可用位置
    		for(i=0; i<1024; i++){
    			if(client[i].fd==-1){
    				client[i].fd = cfd;
    				client[i].events = POLLIN;
    				break;
    			}
    		}
    		//客户端连接数达到最大值
    		if(i==1024){
    			close(cfd);
    			continue;
    		}
    		//修改client数组下标最大值
    		if(maxi<i)
    			maxi = i;
    		if(--nready==0)
    			continue;
    	}
    	//有客户端发送数据的情况
  	for(i=1; i<=maxi; i++){
    		sockfd = client[i].fd;
    		//如果client数组中fd为-1, 表示已经close
    		if(client[i].fd==-1){
    			continue;
    		}
    		if(client[i].revents==POLLIN){
    			n = read(sockfd, buf, sizeof(buf));
    			if(n<=0){
    				close(sockfd);
    				client[i].fd = -1;		
    			}
    			else 
    				//数据处理
  	  		if(--nready==0){
  	  			break;
  	  		}	
    		}  		
  	}  	
    }
  close(lfd); 
  ```

## 3 epoll多路IO转接

- epoll事件

  - ET模式(边沿触发)

    > 缓冲区剩余未读尽的数据不会导致 epoll_wait 返回，新的事件满足时才会触发
    >
    > epoll的ET模式，是高效模式，但是只支持非阻塞模式

    ```
    struct epoll_event event;
    event.data.fd = connfd;
    event.events = EPOLLIN | EPOLLET;
    int flg = fcntl(connfd, F_GETFL);
    flg |= O_NONBLOCK;
    fcntl(connfd, F_SETFL, flg);
    epoll_ctl(epfd, EPOLL_CTL_ADD, connfd， &event);
    ```

  - LT模式(水平触发)

    > 默认采用模式，缓冲区剩余未读尽的数据会导致 epoll_wait 返回

## 4 epoll相关函数

- 创建一棵监听红黑树

  ```
  int epoll_create(int size);	
  --size：	创建的红黑树的监听节点数量(仅供内核参考，必须传递一个大于0的数)
  函数返回值
  	成功：指向新创建的红黑树的根节点的fd
  	失败： -1，设置errno
  ```

- 操作监听红黑树

  ```
  int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
  --epfd：epoll_create 函数的返回值
  --op：对该监听红黑数所做的操作
  	EPOLL_CTL_ADD 添加fd到监听红黑树
  	EPOLL_CTL_MOD 修改fd在监听红黑树上的监听事件
  	EPOLL_CTL_DEL 将一个fd从监听红黑树上摘下（取消监听）
  --fd：待监听的fd
  --event：本质为struct epoll_event 结构体地址
  函数返回值
  	成功：0
  	失败： -1，设置errno
  ```

  - struct epoll_event 结构体

    - 成员 events：

      EPOLLIN / EPOLLOUT / EPOLLERR

    - 成员 data：联合体

      int fd;	  	//对应监听事件的fd

      void *ptr;

      uint32_t u32;

      uint64_t u64;

- 阻塞监听，等待内核返回事件发生

  ```
  int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
  --epfd：epoll_create 函数的返回值（epoll树根）
  --events：传出参数(数组)， 满足监听条件的fd的结构体
  --maxevents：数组元素的总个数
  --timeout：
  	-1：阻塞
  	=0：不阻塞
  	>0：超时时间 （毫秒）
  函数返回值
  	>0：满足监听的总个数，可以用作循环上限
  	=0：没有fd满足监听事件
  	-1：失败，设置errno
  ```

- epoll模型

  ```
  lfd = socket();						//监听连接事件lfd
  bind();
  listen();
  int epfd = epoll_create(1024);		//epfd, 监听红黑树的树根。
  struct epoll_event tep;				//tep用来设置单个fd属性，
  struct epoll_event ep[1024];		//ep是 epoll_wait() 传出的满足监听事件的数组
  tep.events = EPOLLIN;				//初始化lfd的监听属性
  tep.data.fd = lfd;
  epoll_ctl(epfd， EPOLL_CTL_ADD, lfd, &tep);		//将lfd添加到监听红黑树上。
  while(1) {
  	ret = epoll_wait(epfd, ep, 1024, -1);		//实施监听
  	for (i = 0; i < ret; i++) {
  		// lfd满足读事件，即有新的客户端连接请求
  		if (ep[i].data.fd == lfd) {
  			cfd = Accept();
  			tep.events = EPOLLIN;	//初始化 cfd的监听属性。
  			tep.data.fd = cfd;
  			epoll_ctl(epfd， EPOLL_CTL_ADD, cfd, &tep);
  		} 
  		// cfd满足读事件，即有客户端写数据来
  		else {		
  			n = read(ep[i].data.fd, buf, sizeof(buf));
  			if (n == 0) {
  				close(ep[i].data.fd);
  				// 将关闭的cfd，从监听树上摘下
  				epoll_ctl(epfd,EPOLL_CTL_DEL,ep[i].data.fd , NULL);	
  			} 
  			else if(n > 0) {
  				// 客户端数据处理
  			}
  		}
  	}
  }
  ```

## 5 epoll反应堆模型

> epoll ET模式 + 非阻塞、轮询 + void *ptr

- epoll反应堆的核心思想
  - 在调用epoll_ctl函数的时候，将events上树的时候，利用epoll_data_t的ptr成员，将一个文件描述符、事件和回调函数封装成一个结构体，让ptr指向这个结构体
  - 调用epoll_wait函数返回的时候, 可以得到具体的events
  - 然后获得events结构体中的events.data.ptr指针
  - ptr指针指向的结构体中有回调函数, 最终可以调用这个回调函数

- 反应堆

  ```
  socket、bind、listen -- epoll_create 创建监听红黑树 -- 返回 epfd 
  -- epoll_ctl() 向树上添加一个监听fd --
  -- while(1) --
  -- epoll_wait 监听 -- 对应监听fd有事件产生 -- 返回监听满足数组 --
  -- 判断返回数组元素 
  -- lfd满足 -- Accept 
  -- cfd 满足 -- read() -- 数据处理 
  -- cfd从监听红黑树上摘下 -- EPOLLOUT 
  -- 回调函数 -- epoll_ctl() -- EPOLL_CTL_ADD 重新放到红黑上监听写事件
  -- 等待 epoll_wait 返回 -- 说明 cfd 可写 -- write()回去 
  -- cfd从监听红黑树上摘下 -- EPOLLIN 
  -- epoll_ctl() -- EPOLL_CTL_ADD 重新放到红黑上监听读事件 
  -- epoll_wait 监听--
  ```

