c/c++中存在三种传值方式，在局部函数中，对这三种传值方式传入的参数进行修改，会得到不同的结果。具体见下例：

```c++
#include <stdlib.h>
#include <stdio.h>

static int dummny = 10000;

// 传值(传过来的是原始值的副本，因此下列修改并不会修改原始值)
void change_value(int val)
{
	printf("address of val: %p\n", &val); // 通过地址打印查看形参和原始实参的地址并不相同
	val = dummny; // 实际修改的是一个副本
}

// 传指针 (只是把地址的值做了修改，而非修改指向的内容)
void change_ptr_0(int *val)
{
	printf("address of val: %p\n", &val);
	val = &dummny; // 此处的指针也是一个副本，该副本和实参指针指向的是同一块地址。
				   // 此处仅仅是修改了副本指针的指向，没有修改指向内存的值.
    printf("in change_ptr_0: %d\n", *val);
}

// 传指针 (首先解地址，然后修改指针指向的内容)
void change_ptr_1(int *val)
{
	*val = dummny; // 修改指针指向的内容
	printf("in change_ptr_1: %d\n", *val);
}

// 传指针的地址
void change_ptr_2(int** val)
{
	printf("*val in chang e_ptr_add: %p\n", *val);
	static int target = 100;
	*val = &target;
	printf("*val in change_ptr_add: %p\n", *val);
	printf("**val in change_ptr_add: %d\n", **val);
}

// 传引用
void change_refer(int &val)
{
	val = 666;
	printf("in change_refer: %d\n", val);
}



int main()
{
	int a = 10;
	printf("address of a: %p\n", &a);
	change_value(a); // 传入的只是一个副本，没有修改a的值
	printf("value of a: %d\n", a); // 10
	
	int *p = &a;
	change_ptr_0(p); // 传入指针, 没有修改p的指向，也没有修改a的值
	printf("after change_ptr_0: %d\n", *p);
	printf("value of a: %d\n", a);
	change_ptr_1(p); // 传入指针， 没有修改p的指向，但是修改了p指向内存的内容，也修改了a的值
	printf("after change_ptr_1: %d\n", *p);
	printf("value of a: %d\n", a);

	
	change_ptr_2(&p); // 传入指针的地址， 只是修改了p的指向，但是a的值不会有改变
	printf("poniter of p: %d\n", *p); 
	printf("value of a: %d\n", a);

	change_refer(a);
	printf("value of a after change_refer: %d\n", a);
	return 0;
}

```

