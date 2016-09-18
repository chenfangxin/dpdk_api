# DPDK与接口交互

#### DPDK接管接口

##### DPDK中的接口驱动

DPDK中，接口驱动代码放在`drivers/net`目录下，编译后形成对应的`librte_pmd_XXX.a`库文件。

+ 注册PMD驱动

以IGB驱动为例。接口驱动通过`PMD_REGISTER_DRIVER`宏注册。这个宏利用`__attribute__((constructor,used))`，在程序主函数`main`执行前，执行注册函数`rte_eal_driver_register`，将PMD驱动结构体`struct rte_driver pmd_igb_drv`注册到全局链表`struct rte_driver_list dev_driver_list`中。

IGB的PMD驱动结构体如下:
```
struct rte_driver pmd_igb_drv = {
	.type = PMD_PDEV,
	.init = rte_igb_pmd_init,	// IGB驱动初始化函数
};
```

+ 注册以太网驱动

在`rte_eal_init`函数中，调用`rte_eal_dev_init`函数，会遍历`dev_driver_list`，逐个调用`driver->init`函数(对于IGB驱动，就是`rte_igb_pmd_init`函数)。该函数中，调用`rte_eth_driver_register`函数，将`rte_igb_pmd.pci_drv`成员注册到全局链表`struct pci_driver_list pci_driver_list`中。

IGB的以太网驱动结构体如下：
```
struct eth_driver rte_igb_pmd = {
	.pci_drv = {
		.name = "rte_igb_pmd",
		.id_table = pci_id_igb_map,
		.drv_flags = RTE_PCI_DRV_NEED_MAPPING | RTE_PCI_DRV_INTR_LSC | RTE_PCI_DRV_DETACHABLE,
		.devinit = rte_eth_dev_init, 
		.devuninit = rte_eth_dev_uninit,
	},
	.eth_dev_init = eth_igb_dev_init,
	.eth_dev_uninit = eth_igb_dev_uninit,
	.dev_private_size = sizeof(struct e1000_adapter),
};
```

+ 扫描PCI总线

在`rte_eal_init`函数中，调用`rte_eal_pci_init`函数，扫描PCI总线，获取总线上所有设备的信息，并串在全局链表`struct pci_device_list pci_device_list`中。扫描PCI总线的过程，其实是遍历`/sys/bus/pci/devices`目录，生成并初始化PCI设备对应的结构体`struct rte_pci_device`。

`struct rte_pci_device`的主要成员如下：
```
struct rte_pci_device{
	TAILQ_ENTRY(rte_pci_device) next;
	struct rte_pci_addr addr;

	struct rte_intr_handle intr_handle;

	struct rte_pci_driver *driver;
	...
};
```

+ 执行接口设备初始化函数

在`rte_eal_init`函数中，调用`rte_eal_pci_probe`函数。该函数遍历`pci_device_list`链表，并根据设备的`vendor_id`和`device_id`等信息，找到对应的驱动结构，并调用其中的`devinit`函数(即`rte_eth_dev_init`函数)。 `rte_eth_dev_init`函数中，会调用以IGB太网驱动结构体中的`eth_dev_init`函数(即`eth_igb_dev_init`函数)，初始化IGB接口的收发包相关功能。

`struct rte_eth_dev`的主要成员如下：
```
struct rte_eth_dev {
	eth_rx_burst_t rx_pkt_burst; // eth_igb_recv_pkts
	eth_tx_burst_t tx_pkt_burst; // eth_igb_xmit_pkts

	const struct eth_dev_ops *dev_ops; // eth_igb_ops

	struct rte_eth_dev_cb_list link_intr_cbs;
	...
};

```

调用`rte_intr_callback_register`函数，注册PCI设备的中断处理函数(即`eth_igb_interrupt_handler`)。

#### 收发包过程


#### 接口状态


