# L3FWD-POWER的唤醒机制

L3FWD-POWER的主线程`main_loop`阻塞在`intr_handle->efds[efd_idx]`上。注册过程如下：
```
event_register() --> rte_eth_dev_rx_intr_ctl_q() --> rte_intr_rx_ctl() -->rte_epoll_ctl :
    epfd = epoll_create(255);
    ev.event = EPOLLIN | EPOLLPRI | EPOLLET;
    epoll_ctl(epfd, op, intr_handle->efds[efd_idx], &ev);

sleep_until_rx_interrupt() --> rte_epoll_wait :
    epoll_wait(epfd, evs, maxevents, timeout);
```

问题：

+ `main_loop`线程和`eal_intr_thread_main`线程，有可能阻塞在相同的`fd`上，这种场景下，发生中断，会两个线程都唤醒吗？

+ `main_loop`的`event`中有`EPOLLET`，这点与`eal_intr_thread_main`不同


