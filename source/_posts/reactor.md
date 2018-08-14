---
title: Tornado之Reactor
date: 2018-08-14 20:40:17
tags: tornado
---
### Reactor
* 等待文件描述符或者socket的读写状态准备就绪，然后将就绪事件传递给对应的处理器，最后由处理器负责实际的干活。
* 同步IO，就是无论怎么样在从内核空间复制数据到用户空间的时候一定是同步的
* 具体实现的库就是libevent/libev/libuv/ZeroMQ....各种，当然还有我写这个的初衷Tornado框架了

### Proactor
* 只负责发起异步读写操作,IO操作由操作系统本身来完成

这里得吐槽下linux的AIO确实是没人家windows的异步设计的好

由于这个问题Proactor就不展开讨论了。只讨论Reactor，很多基于事件循环的框架和库都是
Reactor模型的。

### Tornado ioloop
Tornado的ioloop就是实现了Reactor模型，对I/O多路复用的封装，将所有要处理的
I/O事件注册到一个中心I/O多路复用器上，同时主线程/进程阻塞在多路复用器上
一但I/O事件到来或是准备就绪，多路复用器返回事先注册好的相应I/O事件分发到
对应的处理器中。

### I/O多路复用
Linux 下有五种 I/O 模型：
* 阻塞式 I/O
* 非阻塞式 I/O
* I/O 复用（select 和 poll，epoll）
* 信号驱动式 I/O（SIGIO）
* 异步 I/O（AIO）

这里其它不讨论只讨论I/O复用

### select， poll

​    使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读，这一过程会被阻塞，当某一   个套接字可读时返回。之后再使用 recvfrom 把数据从内核复制到进程中。它可以让单个进程具有处理多个 I/O 事件的能力。又被称为 Event Driven I/O，即事件驱动 I/O。

由于select的文件描述符集结构是数组，所以定长为1024，除非改`FD_SETSIZE`后重新编译。

poll是链表，所以文件描述符数目不限，而且对描述符的重复利用比select高。

如果一个线程对某个描述符调用了 select 或者 poll，另一个线程关闭了该描述符，会导致调用结果不确定。

select 和 poll 每次调用都需要将全部描述符从应用进程缓冲区复制到内核缓冲区。

select 和 poll 的返回结果中没有声明哪些描述符已经准备好，所以如果返回值大于 0 时，应用进程都需要使用轮询的方式来找到 I/O 完成的描述符。

#### epoll

epoll 仅适用于 Linux OS。

epoll 比 select 和 poll 更加灵活而且没有描述符数量限制。

epoll_ctl() 用于向内核注册新的描述符或者是改变某个文件描述符的状态。已注册的描述符在内核中会被维护在一棵红黑树上，通过回调函数内核会将 I/O 准备好的描述符加入到一个链表中管理，进程调用 epoll_wait() 便可以得到事件完成的描述符

epoll 只需要将描述符从进程缓冲区向内核缓冲区拷贝一次，并且进程不需要通过轮询来获得事件完成的描述符。

epoll 对多线程编程更有友好，一个线程调用了 epoll_wait() 另一个线程关闭了同一个描述符也不会产生像 select 和 poll 的不确定情况。



epoll 的描述符事件有两种触发模式：LT（level trigger）和 ET（edge trigger）。

##### LT

当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking。

##### ET

和 LT 模式不同的是，通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。

很大程度上减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高。只支持 No-Blocking，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。



谈及LT和ET是因为Tornado在未降级到select的时候使用的epoll，并且是LT模式的。select由于几乎所有的系统都支持，所以在没有epoll的UNIX系统会使用kqueue，如果epoll或者kqueue都不存在那么tornado会降级为select I/O复用。