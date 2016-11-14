# 编译DPDK

编译DPDK的过程：

1) 修改`common_base`和`common_linuxapp`文件
2) 设置环境变量`RTE_SDK`, `RTE_KERNELDIR`
3）设置DPDK Taget `ARCH-MACHINE-EXECENV-TOOLCHAIN`, 比如 `x86_64-native-linuxapp-gcc`
```
	make config T=x86_64-native-linuxapp-gcc
	make 
	make install T=x86_64-native-linuxapp-gcc
```


编译APP：
```
cd examples/l3fw
make V=1
```

`l3fwd`中的编译宏`__SSE4_1__`在哪儿定义的？
	
