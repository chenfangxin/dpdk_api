# DPDK与内核交互

DPDK与内核的交互，依赖`kni`模块，结构如下图所示：

![DPDK KNI](pics/dpdk_kni.png)

例程`kni`演示了DPDK与内核协议栈的交互。

`kni模块`会创建内核线程。

