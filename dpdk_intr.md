# DPDK的处理中断

## 设备中断处理流程

#### 在`rte_eal_intr_init()`函数中

+ 初始化`intr_sources`链表。所有UIO设备的中断都会串在这个链表上(在`rte_intr_callback_register`函数中)。
+ 创建`intr_pipe`管道
+ 创建`intr_thread`线程，其执行体是`eal_intr_thread_main`函数

#### 在`intr_thread`线程中

```
for(;;) {
	struct epoll_event pipe_event;
	int pfd = epoll_create(1);
	pipe_event.events = EPOLLIN | EPOLLPRI;
	pipe_event.data.fd = intr_pipe.readfd;
	epoll_ctl(pfd, EPOLL_CTL_ADD, intr_pipe.readfd, &pipe_event);
	totalfds++;

	TAILQ_FOREACH(src, &intr_sources, next) {
		ev.events = EPOLLIN | EPOLLPRI;
		ev.data.fd = src->intr_handle.fd;
		epoll_ctl(pfd, EPOLL_CTL_ADD, src->intr_handle.fd, &ev);
		totalfds++;
	}

	for(;;){
		nfds = epoll_wait(pfd, events, totalfds, -1);
		if(nfds==0){
			continue;
		}
		eal_intr_process_interrupts(events, nfds);
	}
	close(pfd);
}
```

> `epoll_create(size)`调用中，`size`的大小在`2.6.8`之后的内核中已经被忽略了，但是必须大于0

`intr_pipe`作为重建监听列表的标记，每次执行`rte_intr_callback_register`注册新的设备，就会写`writefd`，这样`intr_pipe.readfd`就会有事件，表明要重新扫描`intr_sources`，重建监听列表。

#### 在`eal_intr_process_interrupts`函数中
+ 读取`events[n].data.fd`， 获得中断发生的次数
+ 逐个调用`intr_sources.callbacks`中注册的中断处理函数，这些函数是通过`rte_intr_callback_register`注册的

------------------------------------------------------------
DPDK中，中断分为网卡接收中断，网卡状态中断。

`struct rte_pci_device`结构中的成员`struct rte_intr_handle intr_handle`，用于处理设备中断。

```
struct rte_intr_handle {
	union{
		int vfio_dev_fd;
		int uio_cfg_fd;
	};
	int fd;
	enum rte_intr_handle_type type;
	uint32_t max_intr;
	uint32_t nb_efd;
	int efds[RTE_MAX_RXTX_INTR_VEC_ID];
	struct rte_epoll_event elist[RTE_MAX_RXTX_INTR_VEC_ID];
	int *intr_vec;
};

```
各成员用途如下：

+ `uio_cfg_fd :` 访问`/sys/class/uioX/device/config`文件的句柄，在`pci_uio_alloc_resource`函数中初始化
+ `fd :` 访问`/dev/uioX`文件的句柄，在`pci_uio_ioport_map`函数中初始化
+ `type :` 中断的类型(定义于`eal_interrupts.h`)
+ `max_intr :` 接口使用的中断个数。由`pci_uio_efd_enable`函数可知，除了`VFIO_MSIX`类型的中断，其他类型的中断，所有RX Queue共用一个中断。
+ `nb_efd :` 关注的RX Interrupt数量(即RX Queue数)，除了`VFIO_MSIX`类型的中断，其他类型的中断共用一个fd
+ `efds :` RX Interrupt对应的句柄(fd)，在`rte_intr_efd_enable`中初始化
+ `elist :`
+ `intr_vec :` 在接口驱动中初始化，该数组大小由接口的RX Queue个数决定

`struct rte_epoll_event` 结构体定义如下：
```
struct rte_epoll_event{
	volatile uint32_t status;
	int fd;
	int epfd;
	struct rte_epoll_data epdata;
};

struct rte_epoll_data{
	uint32_t event;
	void *data;
	rte_intr_event_cb_t cb_func;
	void *cb_arg;
};
```


## 网卡接收中断
`port_conf.rxq` 用于控制是否使用接收中断。在`dev->dev_ops->dev_start`函数中，根据此参数配置中断。

## 网卡状态中断
`port_conf.lsc` 用于控制是否使用接口状态中断

--------------------

## Interrupt mode PMD的实现

使用`Interrupt mode PMD`能在没有包处理时，使APP睡眠，这样CPU占用率就不会总是100%了。

