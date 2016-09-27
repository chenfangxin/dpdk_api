# The Userspace I/O HOWTO

原文见[The Userspace I/O HowTo](https://www.kernel.org/doc/htmldocs/uio-howto/)

## About UIO

### How UIO Works

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

