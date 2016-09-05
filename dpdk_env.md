# 使用环境设置

设置DPDK应用程序的运行环境，分为如下几个部分：

1. 加载内核模块
2. 设置Hugepage
3. 接管物理口

## 加载内核模块

DPDK依赖`UIO`或`VFIO`在用户空间访问物理口，将要接管的物理口绑定(Bind)到`igb_uio`，`uio_pci_generic`或`vfio-pci`驱动上。

使用`igb_uio`
```
modprobe uio
insmod DPDK_BUILD/kmod/igb_uio.ko
insmod DPDK_BUILD/kmod/rte_kni.ko
```

## 设置Hugepage

有的平台支持`1GB`的大页，有的只支持`2M`的大页。从`/proc/cpuinfo`文件中的`flag`来判断：
```
cat /proc/cpuinfo | grep pdpe1gb --> 支持1GB的大页
cat /proc/cpuinfo | grep pse --> 支持2MB的大页
```

用如下命令设置各`NUMA Node`上，各中规格的大页的数量：

```
echo 256 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages

mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge
```

## 接管物理口
```
./tools/dpdk_nic_bind.py --bind=igb_uio eth4
./tools/dpdk_nic_bind.py --bind=igb_uio eth5
```
