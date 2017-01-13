# 分析L2FWD_FORK例程

这个例程是一个多进程运行模型，其启动和运行方式，可以用在BOS/TSOS中。

这个例程采用`master-slave`方式运行，`master`为主进程，在每个CORE上创建一个线程，然后在线程中`fork`出`slave`进程。

#### 启动过程


