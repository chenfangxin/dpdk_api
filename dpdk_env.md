# 使用环境设置

设置DPDK应用程序的运行环境，分为如下几个部分：

1. 内核的设置
2. 设置Hugepage
3. 接管物理口

## 内核的设置
DPDK依赖一些内核特性，需要在编译内核时打开相关选项，例如：`HPET`, `HPET_MMAP`，`HUGETLBFS`, `PROC_PAGE_MONITOR`，`IOMMU`，`UIO`或`VFIO`。


DPDK依赖`UIO`或`VFIO`在用户空间访问物理口，将要接管的物理口绑定(Bind)到`igb_uio`，`uio_pci_generic`或`vfio-pci`驱动上。

使用`igb_uio`内核模块
```
modprobe uio
insmod DPDK_BUILD/kmod/igb_uio.ko
insmod DPDK_BUILD/kmod/rte_kni.ko
```
当使用`igb_uio`时，为了配合使用`Intel vt-d`，需要内核启动命令行中，要加上`iommu=pt`；若使用`vfio-pci`或`uio_pci_generic`时，内核启动命令行可以加上`iommu=pt`或`iommu=on`。

> 设置iommu=pt意即 pass through iotlb translation


## 设置Hugepage

有的平台支持`1GB`的大页，有的只支持`2M`的大页。从`/proc/cpuinfo`文件中的`flag`来判断：
```
cat /proc/cpuinfo | grep pdpe1gb --> 支持1GB的大页
cat /proc/cpuinfo | grep pse --> 支持2MB的大页
```

Linux内核缺省的`hugepage`是`2MB`, 在支持`1GB`大页的平台上，需要手动添加启动参数：
```
default_hugepagesz=1G hugepagesz=1G hugepages=4
```

用如下命令设置各`NUMA Node`上，各种规格的大页的预留数量：

```
echo 256 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
echo 256 > /sys/devices/system/node/node1/hugepages/hugepages-2048kB/nr_hugepages

mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge
```

## 接管物理口

```
./tools/dpdk_nic_bind.py --bind=igb_uio eth4
./tools/dpdk_nic_bind.py --bind=igb_uio eth5
```

DPDK使用的UIO相关文件如下：

```
/sys/bus/pci/devices
/dev/uioX
/sys/class/uio/uioX/maps/mapX
/sys/class/uio/uioX/portio/portX
```

