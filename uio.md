# 使用UIO

原文见[The Userspace I/O HowTo](https://www.kernel.org/doc/htmldocs/uio-howto/)

## UIO介绍

### 用UIO接管接口
首先加载`uio`模块， 使用如下命令：

```
modprobe uio
insmod igb_uio
```

假设接口`eth0`的类型为`e1000e`，其PCI地址为`0000:06:00.0`，先使用如下命令解除接口与原驱动的绑定：
```
echo 0000:06:00.0 > /sys/bus/pci/devices/0000:06:00.0/driver/unbind
```
或
```
echo 0000:06:00.0 > /sys/bus/pci/drivers/e1000e/unbind
```
然后用如下命令，将接口绑定到`igb_uio`：
```
echo "8086 150c" > /sys/bus/pci/drivers/igb_uio/new_id
echo 0000:06:00.0 > /sys/bus/pci/drivers/igb_uio/bind
```

这样会在`/dev`目录下，生成一个新的设备文件`uioX`，X表示绑定到`uio`的设备顺序。

### UIO驱动的工作方式

UIO框架如下图所示：

![UIO框架](pics/uio.jpeg)

每个`UIO`设备，都有一个对应的`/dev/uioX`设备文件。程序可以通过`/dev/uioX`文件访问和控制被UIO接管的设备：

+ 通过`mmap`操作，可以将设备的寄存器或内存映射到用户空间，以便程序访问。
+ 通过`read`操作，可以获取设备的中断。

`read`操作是阻塞的，直到有中断发生才会返回。也可先用`select`来等待，然后在调用`read`。`read`返回的结果为中断发生的次数。

某些硬件有多个内部中断源，但是没有分离的IRQ Mask和Status Register(只能通过读取`IRQ Register`来判断中断源)。这种情况下，如果内核通过写`IRQ Register`来关中断，那么用户空间程序无法确定到底哪个中断源发生了中断。

为解决这个问题，`UIO`框架提供了`write()`函数。对于只有一个中断源或者有分离IRQ Mask和Status Register的硬件，可以忽略这个函数。
每次执行对`/dev/uioX`设备的`write`操作，都会调用驱动的`irqcontrol()`函数。写入0，1分别表示关闭或打开中断。
如果驱动没有实现`irqcontrol()`函数，`write()`会返回`-ENOSYS`。

为了以恰当的方式处理中断，内核模块可以提供自己的中断处理函数(Interrupt Handler)，`UIO`框架会自动调用。有些设备并不产生中断，但是却要求实现`poll`，这种情况下，可以用一个定时器以一定的间隔触发中断处理函数。

`UIO`框架在`/sys/class/uio/uioX`目录下，为用户空间程序提供如下标准属性：

* name : 设备的名称
* version : 驱动的版本
* event : 从上次read之后，中断发生的次数

每个`UIO`设备都有一个或多个内存域(memory region)，每个内存域在`sysfs`中，都有一个对应的目录，比如`/sys/class/uio/uioX/maps/map0`，表示第一个内存域。每个`mapX`目录，包含4个只读属性：

* name ：用于标识此内存域的字符串
* addr ：内存域映射的内存地址
* size : 内存域的大小(单位：Byte)
* offset ：此内存域相对于mmap返回值的偏移量(单位：Byte)

对`/dev/uioX`设备的`mmap()`操作，总是返回一个页对齐的地址，而设备的内存域有可能不是页对齐的，此时就可以用`offset`来确定内存域的实际映射地址。

某些硬件包含不能用内存映射方式访问的区域，比如x86平台上的IO端口。用户空间程序可以使用`ioperm()`，`iopl()`，`inb()`，`outb()`等函数访问IO端口。

因为IO端口不能像内存域一样映射，端口信息不会出现在`/sys/class/uio/uioX/maps`目录下，所以用户空间程序很难获知那些IO端口属于某个`UIO`设备。为了解决这个问题，`UIO`框架提供了新的目录：`/sys/class/uio/uioX/portio`，设备的每个端口域(port region)，在该目录下都有一个对应的子目录，比如`port0`，`port1`等。在这些子目录中，同样提供4个只读属性：

* name ：用于标识端口域的字符串
* start ：此端口域的第一个端口
* size ：此端口域中，端口的数量
* portttype ：用于标识端口类型的字符串

## 编写UIO内核模块

以`uio_cif.c`文件为例子，展示如何编写UIO内核模块。

### `struct uio_info`结构体

结构体`struct uio_info`包含设备驱动的细节，其定义在(`include/linux/uio_driver.h`中)，主要成员如下：

```
struct uio_info {
	const char *name;
	const char *version;
	struct uio_mem mem[MAX_UIO_MAPS];
	struct uio_port port[MAX_UIO_PORT_REGIONS];
	long irq;
	unsigned long irq_flags;

	irqreturn_t (*handler)(int irq, struct uio_info *dev_info);
	int (*mmap)(struct uio_info *info, struct vm_area_struct *vma);
	int (*open)(struct uio_info *info, struct inode *inode);
	int (*release)(struct uio_info *info, struct inode *inode);
	int (*irqcontrol)(struct uio_info *info, s32 irq_on);
};

```

### 添加中断处理函数

### Using `uio_pdrv` for platform devices

### Using `uio_pdrv_genirq` for platform devices

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

