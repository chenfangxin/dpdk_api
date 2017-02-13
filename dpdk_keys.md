# DPDK中的优化技术

## 编译器特性

+ 使用`__attribute__`
| 属性             | 作用   |
|------------------|--------|
| constructor      |        |
| always_inline    |        |
| unused           |        |
| noreturn         |        |
| __aligned(N)__   |        |
| __packed__       |        |

## 多线程编程
`rte_lcore_id()`函数，使用了一个线程相关的变量`per_lcore__lcore_id`。该变量是通过`RTE_DEFINE_PER_LCORE`宏定义的，该宏定义如下：
```
#define RTE_DEFINE_PER_LCORE(type, name) \
		__thread __typeof__(type) per_lcore_##name
```

+ `__thread` ：设置静态的TLS变量
+ `__typeof__` ：

## Cache相关

+ 预取
| 函数             | 作用   |
|------------------|--------|
| rte_prefetch0    |        |

## 内存相关


## CPU特性相关

+ [使用SIMD指令](simd.md)

## 系统调度相关


