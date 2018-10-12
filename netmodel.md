#Asynchronous I/O Non-Blocking#
>同步IO非阻塞

##SELECT##
>select是UNIX-like中的一个应用程序接口。

```
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *errorfds, struct timeval *timeout);
``` 


**fd_set**
```
https://elixir.bootlin.com/linux/v4.13.16/source/fs/select.c
// /usr/include/linux/posix_types.h
#define __FD_SETSIZE	1024

// /usr/include/sys/select.h
typdef long int __fd_mask;

#define __NFDBITS (8 * (int)sizeof(__fd_mask))

typedef struct{
	__fd_mask fds_bits[__FD_SETSIZE / __NFDBITS];
}fd_set;
```

fd_set是用于保存文件描述符的一种**bit mask**结构。select能够监听的文件描述符数量与该结构有直接关联。通过fdset的bit位状态与进程的文件表项对应起来，系统得以确认当前需要监听哪些文件的状态。以上所示源码为例，鉴于__FD_SETSIZE通常定义为1024，fd_set实际是一个大小为32的long int数组，bit位总数就是 4*8*32 = 1024。从0开始根据描述符大小将对应的bit位置位1从而获得监听的描述符。所以**select对于文件描述符最大为1024的限制不仅是个数的限制，同时也是值的限制**。
所以select接口要求用户将当前监听的所有文件描述符集合中最大的一个maxfd+1作为第一个参数传入select调用函数，这是select内部对于轮询监听描述符的优化手段。

**select**对比如今的高并发要求已显的比较低效和笨拙。
由于select对于文件描述符的大小和值的限制，虽然可以通过重新编译内核的__FD_SETSIZE值该突破该限制，但依然显得十分多余与低效，使用select必须自行维护3个read、write、exception的fdset结构，select在系统内部将会拷贝这三个结构至内核空间导致内存的浪费。同时在高并发情况下，由于fdset的硬性限制，select无法完成任务。


##POLL##

```
int poll(struct pollfd* fds, nfds_t nfds, int timeout);
```

**pollfd**
```
struct pollfd{
	int fd;  /*file descriptior*/
	short events;  /*requested events*/
	short revents;  /*returned events*/
}
```

**poll**底层与select的实现相差无几，几乎完成了和select一样的工作，不同在于poll摈弃了fdset，采用了pollfd的结构，系统内部不会对该结构作重复拷贝

##EPOLL##

```
int epoll_create(int size);
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
```