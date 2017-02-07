# DPDK与内核交互

DPDK与内核的交互，依赖`kni`模块，结构如下图所示：

![DPDK KNI](pics/dpdk_kni.png)

例程`kni`演示了DPDK与内核协议栈的交互。

`kni`的启动参数示例如下：

```
kni -c 0xf -n 4 -- -P -p 0x3 --config="(0,1,2,3),(1,1,2,3)"
```
config的格式为`(port, lcore_rx, lcore_tx, lcore_kernel_thread)`，分别指定用于接口接收和发送的Core，以及运行内核线程的Core。

上例中，会创建两个虚接口设备（vEth0_0, vEth1_0）和一个内核线程(kni_single)。虚接口的名称由用户空间程序设定。

## `kni模块`的初始化过程

`/dev/kni`是一个字符设备，在`lib/librte_eal/linuxapp/kni/kni_misc.c:kni_init()`中，调用`misc_register()`函数创建。
字符设备对外提供`ioctl`接口，有三个命令：`RTE_KNI_IOCTL_TEST`,`RTE_KNI_IOCTL_CREATE`和`RTE_KNI_IOCTL_RELEASE`。

在`lib/librte_kni/rte_kni.c`中，对`/dev/kni`模块提供的接口，做了进一步的封装，用户空间程序可以调用这些接口，与KNI设备交互。

## 与`kni模块`的交互过程

`kni`设备基于共享内存，在内核与用户空间之间，建立起了FIFO，用于传递报文。

| 函数名                  | 功能                          |
|-------------------------|-------------------------------|
| rte_kni_rx_burst        | 从KNI设备(Kernel)收包         |
| rte_kni_tx_burst        | 向KNI设备(Kernel)发包         |

