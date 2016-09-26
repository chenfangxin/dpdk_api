# DPDK的处理中断

DPDK中，中断分为网卡接收中断，网卡状态中断。

网卡接收中断

网卡状态中断

--------------------

## Interrupt mode PMD的实现

使用`Interrupt mode PMD`能在没有包处理时，使APP睡眠，这样CPU占用率就不会总是100%了。

