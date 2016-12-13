# 一些全局变量

+ `struct internal_config internal_config`
定义于`eal.c`文件。每个进程都有自己的`internal_config`变量，其域分别在`eal_reset_internal_config`，`eal_parse_common_option`和`eal_hugepage_info_init`等函数中初始化。

+ `static struct rte_config rte_config`
通过`/var/run/.rte_config`文件，在各进程之间共享`rte_config.mem_config`结构。

+ `rte_eth_devices[]`
定义在`rte_ethdev.c`文件中。在`rte_eth_dev_allocate`中初始化。


