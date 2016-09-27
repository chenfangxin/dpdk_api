# DPDK的处理中断

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
};
```
各成员用途如下：
`intr_handle.fd :` 访问`/dev/uioX`文件的句柄
`intr_handle.uio_cfg_fd :` 访问`/sys/class/uioX/device/config`文件的句柄


## 网卡接收中断

## 网卡状态中断

--------------------

## Interrupt mode PMD的实现

使用`Interrupt mode PMD`能在没有包处理时，使APP睡眠，这样CPU占用率就不会总是100%了。

