# 在Container中运行DPDK

根据[Running DPDK in a LXC-based Container](dpdk.org/ml/archives/dev/2014-October/006373.html)里的说明，可按如下步骤，在LXC中运行DPDK：

+ Attaching NIC device to the Container

在HOST中，将接口绑定到`igb_uio`，查看`/dev/uioN`文件，`N`表示接口绑定到`uio`的序号。然后允许LXC Container访问该设备文件：

> lxc.cgroup.devices.allow = c 249:N rwm

LXC Container启动后，需要用`mknod`命令，创建设备文件：

> mknod /dev/uio1 c 249 1
> mknod /dev/uio2 c 249 2
> mknod /dev/uio3 c 249 3

+ Host allocated Hugepage access inside Container

假设HOST中，已经将Hugepagefs加载到`/mnt/huge`目录，需要让Container能访问这个目录：

> lxc.mount.entry = /mnt/huge mnt/huge none bind,create=dir 0 0

----------------

