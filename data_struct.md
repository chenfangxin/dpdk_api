# DPDK中的数据结构

## LIST 和 TAILQ
这两个是GLIBC带的数据结构，定义在`/usr/include/sys/queue.h`文件中。

```
#define _TAILQ_HEAD(name, type, qual)	\
	struct name{						\
		qual type *tqh_first;			\
		qual type *qual *tqh_last;		\
	}

#define TAILQ_HEAD(name, type)	_TAILQ_HEAD(name, struct ype, )

#define TAILQ_HEAD_INITIALIZER(head)	\
	{NULL, &(head).tqh_first}

#define _TAILQ_ENTRY(type, qual)		\
	struct name{						\
		qual type *tqe_next;			\
		qual type *qual *tqe_prev;		\
	}
#define TAILQ_ENTRY(type)	_TAILQ_ENTRY(struct type, )
```

#### TAILQ的初始化
* `EAL_REGISTER_TAILQ`宏用来定义一个`constructor`属性的函数
* 该函数中调用`int rte_eal_tailq_register(...)`函数，注册`struct rte_tailq_elem`结构体到全局变量`rte_tailq_elem_head`链表中
* 在`rte_eal_tailq_init()`函数中，遍历整个链表，将其成员赋值给`mem_config.tailq_head`数组，这个数组是多进程共享的。全局变量`rte_tailqs_count`表示已经注册的TAILQ的个数。


