# DPDK内存管理

DPDK内存管理的主要数据结构为`struct rte_mem_cfg`，定义如下：
```
struct rte_mem_config{
	volatile uint32_t magic;

	uint32_t nchannel;
	uint32_t nrank;

	rte_rwlock_t mlock;
	rte_rwlock_t qlock;
	rte_rwlock_t mplock;

	uint32_t memzone_cnt;
	struct rte_memmseg memseg[RTE_MAX_MEMSEG];
	struct rte_memzone memzone[RTE_MAX_MEMZONE];

	struct rte_tailq_head tailq_head[RTE_MAX_TAILQ];

	struct malloc_heap malloc_heaps[RTE_MAX_NUMA_NODES];

	uint64_t mem_cfg_addr;
};
```

+ `memseg:` 表示一段连续的物理内存
```
struct rte_memseg{
	phys_addr_t phys_addr;
	union{
		void *addr;
		uint64_t addr_64;
	};
	size_t len;
	uint64_t hugepage_sz;
	int32_t socket_id;
	uint32_t nchanel;
	uint32_t nrank;
};
```
`rte_config.mem_config.memseg[]`结构在初始化后，在运行过程中，不会再改变。

+ `memzone:`
```
struct rte_memzone{
	char name[32];
	phys_addr_t phys_addr;
	union{
		void *addr;
		uint64_t addr_64;
	};
	size_t len;
	uint64_t hugepage_sz;
	int32_t socket_id;
	uint32_t flags;
	uint32_t memseg_id;
};
```
`rte_config.mem_config.memzone[]`为已经分配出去的内存块，各进程可以通过名字查找。

+ `tailq_head:`
```
struct rte_tailq_head{
	struct rte_tailq_entry_head tailq_head;
	char name[32];
};
```

+ `malloc_heaps:` 为每个NUMA节点建立一个`malloc_heap`
```
struct malloc_elem{
	struct malloc_heap *heap;
	struct malloc_elem *volatile prev;
	LIST_ENTRY(malloc_elem) free_list;
	const struct rte_memseg *ms;
	volatile enum elem_state state;
	uint32_t pad;
	size_t size;
};

struct malloc_heap{
	rte_spinlock_t lock;
	LIST_HEAD(, malloc_elem) free_head[13];
	unsigned alloc_count;
	size_t total_size;
};
```

#### 基于`malloc_heap`的内存管理
在`int rte_eal_malloc_heap_init(void)`函数中，在每个`memseg`的头尾建立`struct malloc_elem`结构，并根据该`memseg`的大小，将其添加到`malloc_heap->free_head[]`中。
其实在分配过程中，会在每个可供分配的内存块的头尾建立`struct malloc_elem`结构，通过该结构将内存块串联在`malloc_heap->free_head`链表中。分配出来的内存块的头尾也会建立`struct malloc_elem`结构，以便释放时，能合并内存块并串入链表。

> `malloc_heap->free_head`的规格，由宏`MALLOC_MINSIZE_LOG2`和`MALLOC_LOG2_INCREMENT`控制，总的规格数为`RTE_HEAP_NUM_FREELIST`。(见`malloc_elem_free_list_index(size)`函数)

在`rte_malloc_socket`函数中，进行基于`malloc_heap`的内存分配，其函数原型如下：
```
void *rte_malloc_socket(const char *type, size_t size, unsigned align, int socket);
```
主要实现函数为`malloc_heap_alloc()`，该函数中，先通过`find_suitable_element()`函数，在`malloc_heaps->free_list[]`中，找到一个合适的`malloc_elem`；然后调用`malloc_elem_alloc()`，对`malloc_elem`进行分裂，得到合适大小的`malloc_elem`，并返回可用地址。

由此可见，基于`malloc_heap`的内存管理，其实是依赖基于`malloc_elem`组成的链表，分配过程就是在链表中找到并截取一块大小合适的内存。

在`rte_free`函数中，释放内存块，其函数原型如下：
```
void rte_free(void *addr);
```
该函数中，先通过`malloc_elem_from_data`，找到要释放的内存块对应的`malloc_elem`结构体，然后调用`malloc_elem_free`函数释放内存块。

> 不管是分配，还是释放内存块的过程，都要加`heap->lock`这个全局锁，由此可见效率是不高的。

#### 基于`memzone`的内存管理
通过`rte_memzone_reserve()`函数创建`memzone`，其实也是基于`malloc_heap`，从其中截取一段内存，然后自己管理。


