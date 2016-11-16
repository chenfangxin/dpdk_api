# DPDK程序模型

## 多线程

## 多进程

在多进程场景下，由于每个进程都有自己的地址空间(Address Space)，进程之间的共享是如何实现的呢？
多个进程中，有一个类型为`PRIMARY`的进程先启动，其会初始化一些共享文件，通过个这些文件在多个进程之间共享信息，主要文件如下：

+ `/var/run/.rte_config`
由`rte_eal_config_create()`函数创建，

`/var/run/.rte_hugepage_info`:
由`rte_eal_hugepage_init()`函数创建，


## Run-to-Complition模式
	就是在一个执行序列(线程/进程)中，完成数据包的接收/处理/发送的全流程。

## Pipeline模式
	数据包的处理分在多个执行序列中进行，

--------------------

示例`load_balancer`演示了多线程，Pipeline模式。



--------------------
示例`client_server_mp`演示了多进程，Pipeline模式

启动`client_server_mp`的命令行：

```
mp_server -c 6 -n 4 -- -p 3 -n 2
mp_client -c 8 -n 4 --proc-type=auto -- -n 0
mp_client -c 10 -n 4 --proc-type=auto -- -n 1
```
EAL参数：
`--proc-type` ：

SPECIAL参数：
`-n` ： 在server命令行中，意为client的个数；在client命令行中，意为client的编号
`-p` ： 在server命令行中，指定接管的NIC

