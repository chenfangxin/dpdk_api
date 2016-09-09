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
EAL参数解析：
+ `-c COREMASK` : 十六进制掩码，指定运行App的Core
+ `-n NUM` : 内存的channel
+ `-b <domain:bus:devid.func>` : 阻止EAL接管的NIC Blacklist
+ `--socket-mem=N,M` : 在指定Numa节点上分配的Hugepage的个数
+ `-m MB` : 指定要从Hugepage分配的总内存大小(建议用`--socket-mem`替代)
+ `-r NUM` : 内存rank数量（缺省情况下会自动探测）
+ `-v` : 显示App的启动信息
+ `--huge-dir` : 指定Hugetlbfs所mount的目录
+ `--file-prefix` : 指定分配hugepage的文件名
+ `--proc-type` : 指定App的类型
+ `-- xen-dom0` :
+ `--vmware-tsc-map`：
+ `--base-virtaddr`：
+ `--vfio-intr`：

> 如何获得当前系统的Channel个数?
> 用命令 dmidecode -t 17 | grep Bank 来获得Channel个数
> 用命令 dmidecode -t 17 | grep Size 来获得各Channel上的内存数

启动`l3fwd`的命令行：
```
l3fwd [EAL options] -- -p PORTMASK
		[-P] [-E] [-L]
		--config (port,queue,lcore)[, (port, queue, lcore)]
		[--eth-dest=X,MM:MM:MM:MM:MM:MM]
		[--enable-jumbo [--max-pkt-len PKTLEN]]
		[--no-numa]
		[--hash-entry-num]
		[--ipv6]
		[--parse-ptype]
```
APP的命令行分为两部分，由`--`分开，左边称为`EAL Options`，右边是App的私有选项。
[EAL options]： DPDK运行环境的配置选项
`-p PORTMASK`：十六进制掩码，指定接管的Port
`[-P]`：将所有接口设为混杂模式(Promiscuous mode)
`[-E]`：使能 Exact match，基于目标IP的完全匹配
`[-L]`：使能 Longest Prefix match，基于路由表的最长掩码匹配
`--config="(port,queue,lcore)"`：指定Port的Queue与Core的对应关系
`--eth-dest=X,MM:MM:MM:MM:MM:MM`：指定Port X的目的MAC
`--enable-jumbo [--max-pkt-len PKTLEN]`：
`--no-numa`：
`--hash-entry-num`：
`--ipv6`：
`--parse-ptype`： 

> Port的顺序可以用`dpdk_nic_bind.py --status`查看

## DPDK APP初始化流程

