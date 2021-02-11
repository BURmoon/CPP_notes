## 1 libevent库

> 开源，精简，跨平台（Windows、Linux、maxos、unix），专注于网络通信
>
> 特性：基于“事件”异步通信模型，即回调

- 源码包安装

  - 检查安装环境，生成 makefile	`./configure`
  - 生成 .o 和 可执行文件	`make`
  - 将必要的资源cp至系统指定目录	`sudo make install`

  > 编译使用库的 .c 时，需要加 -levent 选项
  >
  > 库名 libevent.so ，位于/usr/local/lib

- 未决和非未决

  - 非未决：没有资格被处理

  - 未决：有资格被处理，但尚未被处理

    - event_new --> event ---> 非未决 

      --> event_add --> 未决 

      --> dispatch() && 监听事件被触发 --> 激活态 

      --> 执行回调函数 --> 处理态 

      --> 非未决 event_add && EV_PERSIST --> 未决 

      --> event_del --> 非未决

- libevent框架

  - 创建event_base

    - `struct event_base *event_base_new(void);`

      event_base_new()函数分配并且返回一个新的具有默认设置的 event_base

      函数会检测环境变量，返回一个到event_base的指针，如果发生错误则返回 NULL

    - 选择各种检测事件已经就绪的方法时，函数会选择OS支持的最快方法

  - 创建事件evnet

    - 常规事件event	`event_new();`
    - 带缓冲区的事件bufferevent	`bufferevent_socket_new();`

  - 将事件添加到base上
    
    - `int event_add(struct event *ev, const struct timeval *tv);`
  - 循环监听事件满足
    - `int event_base_dispatch(struct event_base *base);`
    - 一旦有了一个已经注册了事件的event_base，就需要让libevent等待事件并且通知事件的发生
  - event_base_dispatch()将一直运行，直到没有已经注册的事件或者调用停止循环函数为止
  
  - 释放event_base
    - `void event_base_free(struct event_base *base);`
    - 使用完event_base之后,使用event_base_free()进行释放
    - 函数不会释放当前与event_base关联的任何事件，或者关闭套接字，或者释放任何指针

## 2 event相关函数

> libevent的基本操作单元是事件

- 事件的生命周期

  - 调用libevent函数设置事件并且将事件添加event_base之后，事件进入【未决(pending)】状态

  - 在未决状态下，如果触发事件的条件发生(如文件描述符的状态改变)，则事件进入【激活 (active)】状态

    此时(用户提供的)事件回调函数将被执行

  - 如果事件的配置为“持久的 (persistent)”，事件将保持为【未决】状态

    否则，执行完回调后，事件不再是未决的

  - 通过删除操作可以让未决事件成为【非未决(已初始化)】状态

  - 通过添加操作可以让非未决事件再次成为【未决】状态

- 创建事件event

  ```
  struct event *event_new(struct event_base *base, evutil_socket_t fd, short what, 
  				event_callback_fn cb, void *arg);
  --what：
  	EV_READ		读取的时候,事件将成为激活的
  	EV_WRITE	写入的时候,事件将成为激活的
  	EV_PERSIST	表示事件是“持久的”
  回调函数:
  typedef void (*event_callback_fn)(evutil_socket_t fd, short events, void *arg);
  ```

  - event_new()试图分配和构造一个用于base的新的事件

    所有新创建的事件都处于已初始化和非未决状态，调用event_add()可以使其成为未决的

  - 事件被激活时, libevent将调用cb函数, 并传递参数：

    - 文件描述符fd，表示所有被触发事件的位字段，以及构造事件时的arg参数

- 添加事件到event_base(设置未决事件)

  ```
  int event_add(struct event *ev, const struct timeval *tv);
  --ev: event_new()的返回值
  --tv：NULL(事件不会超时)
  ```

  - 构造事件之后，在将其添加到event_base之前是不能对其做任何操作的
  - 在非未决的事件上调用event_add()将使其在配置的 event_base 中成为未决的
  - 如果对已经未决的事件调用event_add()，事件将保持未决状态，并在指定的超时时间被重新调度

- 从event_base上摘下事件(设置非未决事件)

  ```
  int event_del(struct event *ev);
  ```

  - 对已经初始化的事件调用event_del()将使其成为非未决和非激活的
  - 如果事件不是未决的或者激活的,调用将没有效果
  - 如果在事件激活后，其回调被执行前删除事件，回调将不会执行

- 销毁事件

  ```
  void event_free(struct event *event);
  ```

## 3 bufferevent相关函数

> #include <event2/bufferevent.h>
>
> bufferevent由一个底层的传输端口(如套接字 )，一个读取缓冲区和一个写入缓冲区组成

- 创建bufferevent

  ```
  struct bufferevent *bufferevent_socket_new(
  				struct event_base *base, 
  				evutil_socket_t fd, 
  				enum bufferevent_options options);
  --base：event_base
  --fd：封装到bufferevent内的fd
  --options：BEV_OPT_CLOSE_ON_FREE(释放bufferevent时关闭底层传输端口)
  返回值：
  	成功：返回创建的bufferevent事件对象
  ```

  - 使用bufferevent_socket_new()创建基于套接字的bufferevent

    如果想以后设置文件描述符，可以设置fd为-1

- 释放bufferevent

  ```
  void bufferevent_free(struct bufferevent *bev);
  ```

  - 如果释放时还有未决的延迟回调，则在回调完成之前bufferevent不会被删除

- 在bufferevent上启动链接 --socket();connect();

  ```
  int bufferevent_socket_connect(
  			struct bufferevent *bev, 
  			struct sockaddr *address, int addrlen);
  返回值：
  	成功：返回0
  	失败：则返回-1
  ```

  - 如果还没有为bufferevent设置套接字，调用函数将为其分配一个新的流套接字，并且设置为非阻塞的
  - 如果已经为bufferevent设置套接字，调用函数将告知libevent套接字还未连接，直到连接成功之前不应该对其进行读取或者写入操作

- 给bufferevent设置回调

  ```
  void bufferevent_setcb(struct bufferevent * bufev,
  			bufferevent_data_cb readcb,
  			bufferevent_data_cb writecb,
  			bufferevent_event_cb eventcb,
  			void *cbarg );
  --bufev：bufferevent_socket_new()返回值
  --readcb：设置bufferevent读缓冲对应回调
  --writecb：设置bufferevent写缓冲对应回调
  --eventcb：设置事件回调，也可传NULL
  --events：BEV_EVENT_CONNECTED
  --cbarg：上述回调函数使用的参数
  回调函数
  	typedef void (*bufferevent_data_cb)(struct bufferevent *bev, void *ctx);
  	typedef void (*bufferevent_event_cb)(struct bufferevent *bev, short events, void *ctx);
  ```

  - 当bufferevent绑定的socket连接, 断开或者异常的时候触发 eventcb 事件回调

  - 回调函数的第一个参数都是发生了事件的bufferevent，最后一个参数都是cbarg参数，可以通过它向回调传递数据

  - bufferevent的读事件回调触发时机

    - 当数据由内核的读缓冲区到bufferevent的读缓冲区的时候，会触发bufferevent的读事件回调

      数据由内核到bufferevent的过程不是用户程序执行的，是由bufferevent内部操作的

  - bufferevent的写事件回调触发时机

    - 当用户程序将数据写到bufferevent的写缓冲区之后
    - bufferevent会自动将数据写到内核的写缓冲区，最终由内核程序将数据发送出去

- 向bufferevent的输出缓冲区添加/移除数据

  - 将内存中从data处开始的size字节数据添加到输出缓冲区的末尾

    ```
    int bufferevent_write(struct bufferevent *bufev, const void *data, size_t size);
    ```

  - 至多从输入缓冲区移除size字节的数据，将其存储到内存中data处

    ```
    size_t bufferevent_read(struct bufferevent *bufev, void *data, size_t size);
    ```

- 启动、关闭 bufferevent的缓冲区

  ```
  void bufferevent_enable(struct bufferevent *bufev, short events); 
  --events： EV_READ、EV_WRITE、EV_READ|EV_WRITE
  ```

  - 默认write缓冲是enable，read缓冲是disable

- 创建链接监听器 --socket();bind();listen();accept();

  ```
  struct evconnlistener *evconnlistener_new_bind (	
  			struct event_base *base,
  			evconnlistener_cb cb, 
  			void *ptr, 
  			unsigned flags,
  			int backlog,
  			const struct sockaddr *sa,
  			int socklen);
  --base：	event_base
  --cb: 	回调函数 (一旦被回调，说明在其内部应该与客户端完成数据读写操作，进行通信)
  --ptr：	回调函数的参数
  --flags：LEV_OPT_CLOSE_ON_FREE | LEV_OPT_REUSEABLE
  --backlog：listen()参2， -1表最大值
  --sa：服务器自己的地址结构体
  --socklen：服务器自己的地址结构体大小
  返回值：
  	成功创建的监听器
  回调函数:
  	typedef void (*evconnlistener_cb)(struct evconnlistener *evl, evutil_socket_t fd, 
  			struct sockaddr *cliaddr, int socklen, void *ptr);
  ```

  - 根据要绑定到的地址和地址长度，可以调用 evconnlistener_new_bind() 由libevent分配和绑定套接字

  - 连接监听器使用 event_base 来得知什么时候在给定的监听套接字上有新的TCP连接

  - 新连接到达时，监听器调用自己给出的回调函数

    如果cb为NULL，则监听器是禁用的,直到设置了回调函数为止

- 释放监听服务器

  ```
  void evconnlistener_free(struct evconnlistener *lev);
  ```

- libevent服务器端

  - 创建 event_base
  - 创建 bufferevent 事件对象	`bufferevent_socket_new();`
  - 使用 bufferevent_setcb() 函数给 bufferevent 的 read、write、event 设置回调函数
  - 当监听的事件满足时，read_cb会被调用，在其内部进行读操作	`bufferevent_read()`
  - 使用 `evconnlistener_new_bind()` 创建监听服务器， 设置其回调函数
    - 当有客户端成功连接时，回调函数会被调用，在函数内部完成与客户端通信
    - 设置读缓冲、写缓冲的使能状态 enable、disable
  - 启动循环	`event_base_dispath();`
  - 释放连接	`evconnlistener_free(listener);`

