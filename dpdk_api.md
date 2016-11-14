# DPDK常用API

## CPU相关

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_lcore_id              |   |
| rte_lcore_count              |   |

## 接口相关

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_eth_dev_count            |   |


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

## 收发报文

| 函数名                       | 功能                          |
|------------------------------|-------------------------------|
| rte_eth_rx_burst             |    | 
| rte_eth_tx_burst             |    | 
| rte_eth_tx_buffer            |    | 
