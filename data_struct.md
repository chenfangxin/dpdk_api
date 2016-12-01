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

