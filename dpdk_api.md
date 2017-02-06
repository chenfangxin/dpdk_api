# DPDK常用API

## CPU相关

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_lcore_id                 |   |
| rte_lcore_count              |   |
| rte_lcore_to_socket          |   |


## 内存使用

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_mempool_create           |    |
| rte_mempool_lookup           |    |
| rte_mempool_count            |    |
|                              |    |
| rte_pktmbuf_pool_create      |    |
|                              |    |
| rte_ring_create              |    |
| rte_ring_lookup              |    |

## 接口相关

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_eth_dev_count            |   |
| rte_eth_dev_configure        |   |
| rte_eth_rx_queue_setup       |   |
| rte_eth_tx_queue_setup       |   |
| rte_eth_dev_start            |   |
| rte_eth_promiscuous_enable   |   |


## 收发报文

| 函数名                       | 功能                          |
|-------------------------------------------------------|-------------------------------|
| rte_eth_rx_burst(port_id, queue_id, rx_pkts, nb_pkts) | 从port_id和queue_id指定的接口队列中收包  |
| rte_eth_tx_burst             |    |
| rte_eth_tx_buffer            |    |

