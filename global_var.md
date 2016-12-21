# 一些全局变量

+ `struct internal_config internal_config`
定义于`eal.c`文件。每个进程都有自己的`internal_config`变量，其域分别在`eal_reset_internal_config`，`eal_parse_common_option`和`eal_hugepage_info_init`等函数中初始化。

+ `static struct rte_config rte_config`
通过`/var/run/.rte_config`文件，在各进程之间共享`rte_config.mem_config`结构。

+ `rte_eth_devices[]`
定义在`rte_ethdev.c`文件中。在`rte_eth_dev_allocate`中初始化，nb_ports表示接口总数。

+ `struct lcore_config lcore_config[RTE_MAX_LCORE]`
在`rte_eal_cpu_init(）`中初始化。此数组中包含全系统的CPU相关信息，比如个数，序号，所在NUMA Node等。

+ `struct pci_driver_list pci_driver_list`，`struct pci_device_list pci_device_list`

+ `struct rte_intr_source_list intr_sources`
定义在`eal_interrupts.c`文件中，用于存放所有网口的中断处理信息。在`rte_intr_callback_register`函数中，将网口中断注册在此链表中。

