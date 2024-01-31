C和C++中malloc在函数内部和外部的使用时会影响其他函数访问其开辟的内存空间。具体影响见下列示例：

```c++
#include <stdlib.h>
#include <stdio.h>

// 局部函数内部分配内存并赋值
void malloc_in_local_function(char *strs)
{
	printf("address of strs: %p\n", &strs); // 这里的strs是p的一个副本
	strs = (char*)malloc(sizeof(char)*128); // 堆上申请内存
	sprintf(strs, "%s", "malloc_in_local_function");
}

// 局部函数外部分配内存并赋值
void malloc_out_local_function(char *strs)
{
	printf("address of strs: %p\n", &strs); // 这里的strs也是p的一个副本
	sprintf(strs, "%s", "malloc_out_local_function");
}

void test_malloc()
{
	char* p = NULL;
	printf("address of p: %p\n", &p);
	malloc_in_local_function(p);
	printf("address of p: %p\n", p);
	printf("malloc_in_local_function result: %s\n", p);
	
	printf("*****************************************\n");
	p = (char*)malloc(sizeof(char)*128);
	printf("address of p: %p\n", &p);
	malloc_out_local_function(p);
	printf("malloc_out_local_function result: %s\n", p);
}


int main()
{
	test_malloc();
	return 0;
}
```

