## 参数解析

```
struct option{
	const char *name;
	int has_arg; 
	int *flag; // 与val联合决定返回值
	int val;
};
```
+`name`: 长参数名 
+`has_arg`: 是否有参数值，0：不带参数值；1：一定要带参数值；2：可以不带参数值
+`flag`: 若为NULL，则返回与option匹配的val值；若不为NULL，则将val值赋值指定的地址
+`val`: 与`flag`联合使用
