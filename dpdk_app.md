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
+ `-b <domain:bus:devid.func>` :
+ `--socket-mem` : 在指定Numa节点上分配的Hugepage大小
+ `-m MB` : 
+ `-r NUM` : 内存rank数量
+ `-v` :
+ `--huge-dir` : Hugetlbfs所mount的目录
+ `--file-prefix` : 分配hugepage的文件名
+ `--proc-type` : App的类型
+ `-- xen-dom0` :



