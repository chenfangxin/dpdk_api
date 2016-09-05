# DPDK应用程序

## 编译DPDK APP

```
export RTE_SDK=
cd my_rte_app
export RTE_TARGET
make

```

## 启动DPDK APP

```
rte-app -c COREMASK [-n NUM] [-b <domain:bus:devid.func>] \
	[--socket-mem=MB,...] [-m MB] [-r NUM] [-v] [--file-prefix] \
	[--proc-type <primary | secondary | auto>] [-- xen-dom0]

```
参数解析：
+ `-c COREMASK` : 十六进制掩码，指定运行App的Core
+ `-n NUM` : 内存的channel
+ `-b <domain:bus:devid.func>` : NIC的黑名单
+ `--socket-mem` : 在指定Numa节点上分配的Hugepage大小
+ `-m MB` : 指定要从Hugepage分配的总内存大小(建议用`--socket-mem`替代)
+ `-r NUM` : 内存rank数量
+ `-v` : 显示App的启动信息
+ `--huge-dir` : 指定Hugetlbfs所mount的目录
+ `--file-prefix` : 指定分配hugepage的文件名
+ `--proc-type` : 指定App的类型
+ `-- xen-dom0` :

