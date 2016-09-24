# DPDK程序模型

## 多线程

## 多进程

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
`-p` ： 
`-n` ： 
