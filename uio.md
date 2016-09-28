# 使用UIO

原文见[The Userspace I/O HowTo](https://www.kernel.org/doc/htmldocs/uio-howto/)

## UIO介绍

### 用UIO接管接口
首先加载`uio`模块， 使用如下命令：

> modprobe uio
> insmod igb_uio

假设接口`eth0`的类型为`e1000e`，其PCI地址为`0000:06:00.0`，先使用如下命令解除接口与原驱动的绑定：

> echo 0000:06:00.0 > /sys/bus/pci/devices/0000:06:00.0/drivers/unbind

或

> echo 0000:06:00.0 > /sys/bus/pci/drivers/e1000e/unbind

然后用如下命令，将接口绑定到`igb_uio`：
> echo "8086 150c" > /sys/bus/pci/drivers/igb_uio/new_id
> echo 0000:06:00.0 > /sys/bus/pci/drivers/igb_uio/bind

这样会在`/dev`目录下，生成一个新的设备文件`uioN`，N表示绑定到`uio`的设备顺序。

## Writing your own kernel module

### struct uio_info

### Adding an interrupt handler

### Using uio_pdrv for platform devices

### Using uio_pdrv_genirq for platform devices

### Using uio_dmem_genirq for platform devices


## Writing a driver in userspace

### Getting information about your UIO device

### mmap() device memory

### Waiting for interrupts

## Generic PCI UIO driver

### Making the driver recognize the device

### Things to know about uio_pci_generic

### Writing userspace driver using uio_pci_generic

### Example code using uio_pci_generic

