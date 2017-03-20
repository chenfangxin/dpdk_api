# DPDK与接口交互

#### DPDK的接口驱动

DPDK中，接口驱动代码放在`drivers/net`目录下，编译后形成对应的`librte_pmd_XXX.a`库文件。

+ 注册PMD驱动

以IGB驱动为例。接口驱动通过`RTE_PMD_REGISTER_PCI`宏和`RTE_PMD_REGISTER_PCI_TABLE`宏注册。

> `RTE_PMD_REGISTER_PCI`宏利用了GCC的`__attribute__((constructor,used))`属性。在程序主函数`main`执行前，定义并执行注册函数`pciinitfn_net_e1000_igb`。

在该函数中，调用`rte_eal_pci_register`函数，将`IGB`的PCI驱动结构体`struct eth_driver rte_igb_pmd->pci_drv`，注册到全局链表`struct pci_driver_list pci_driver_list`中；调用`rte_eal_driver_register`函数，将`pci_drv->driver`注册到链表`struct rte_driver_list dev_driver_list`中。

在`include/rte_pci.h`中定义`struct pci_driver_list`：
```
TAILQ_HEAD(pci_driver_list, rte_pci_driver);

struct rte_pci_driver{
	TAILQ_ENTRY(rte_pci_driver) next;
	struct rte_driver driver;
	pci_probe_t *probe;
	pci_remove_t *remove;
	const struct rte_pci_id *id_table;
	uint32_t drv_flags;
};

```

+ 注册以太网驱动

在`rte_eal_init`函数中，调用`rte_eal_pci_probe`函数，进而调用`pci_probe_all_drivers`函数，遍历`pci_driver_list`链表，查找设备对应的驱动，并调用`struct rte_pci_driver`中的`probe`函数。

IGB的以太网驱动结构体如下：
```
struct eth_driver rte_igb_pmd = {
	.pci_drv = {
		.id_table = pci_id_igb_map,
		.drv_flags = RTE_PCI_DRV_NEED_MAPPING | RTE_PCI_DRV_INTR_LSC | RTE_PCI_DRV_DETACHABLE,
		.probe = rte_eth_dev_pci_probe,
		.remove = rte_eth_dev_pci_remove,
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
示例`link_status_interrupt`演示了监控接口状态变化。

#### 接口信息及统计



