```cpp
    // int类型：栈上申请内存
    int i = 10; 
    // 字符数组：栈上申请内存
    char name[6] = "hello"; 
    // 普通指针：指针堆上申请内存
    char *name_ptr = (char*)malloc(sizeof(char*) * 10); 
    // 指针数组：栈上申请内存空间
    char *name_arrs[6] = {"hello", "world"};
    // 字符常量区申请内存
    char *name_ptr2 = "hello"; 
    // 指针数组, 数据开辟在栈区
    char *ptr_arrs[10] = {"hello", "world"}; 
    char arrs[4] = {'1', '2', '3', '4'};
    // 数组指针：内存开辟在栈区
    char (*arrs_ptr)[4] = &arrs;  
```

# 1. 指针数组内存申请位置

(1) **在函数内部作为局部变量**：
如果指针数组是在某个函数的内部作为局部变量声明的，那么它通常会被分配在栈（stack）上。其生命周期是从声明开始到函数返回结束。

```c
void function() {  
    int *array[10]; // 指针数组在栈上  
    // ...  
} // 当函数返回时，array的存储空间被释放
```

(2) **在全局/静态作用域**：
如果指针数组是在全局作用域或静态作用域中声明的，那么它通常会被分配在静态存储区（static storage area）。这意味着它的生命周期是整个程序的执行期间。

```c
int *globalArray[10]; // 指针数组在静态存储区  
 
void function() {  
    // 可以访问globalArray  
}
```

(3) **在堆上动态分配**：
虽然指针数组本身可能被分配在栈上或静态存储区，但指针数组所指向的数据（如果有的话）可能需要在堆（heap）上动态分配。这通常通过`malloc`、`calloc`或`realloc`等函数实现。

```c
int **array = malloc(10 * sizeof(int *)); // 分配指针数组的存储空间  
if (array == NULL) {  
    // 错误处理  
}  
for (int i = 0; i < 10; i++) {  
    array[i] = malloc(sizeof(int)); // 分配每个指针所指向的int的存储空间  
    if (array[i] == NULL) {  
        // 错误处理，并释放之前已分配的内存  
    }  
    // 初始化array[i]指向的int  
}  
// ...  
// 释放内存  
for (int i = 0; i < 10; i++) {  
    free(array[i]);  
}  
free(array);
```

在这个动态分配的例子中，`array`本身（即指针数组的存储空间）是在堆上分配的，但每个`array[i]`所指向的`int`的存储空间也是单独在堆上分配的。

(4) **在结构体或类中**：
如果指针数组是结构体或类的一个成员，那么它的存储位置将取决于该结构体或类的实例是在哪里和如何被声明的。如果结构体或类的实例是在栈上分配的（如作为函数中的局部变量），则指针数组也将在栈上。如果实例是在堆上分配的，则指针数组也将在堆上。

# 2. 数组指针内存申请位置

(1) **数组指针指向静态数组**：
如果数组指针指向一个静态数组（在全局/静态作用域中定义的数组），那么数组本身是在静态存储区分配的。

```c
int staticArray[10];  
int (*arrayPtr)[10] = &staticArray; // arrayPtr是指向静态数组的指针
```

(2) **数组指针指向动态分配的数组**：
如果数组指针指向一个动态分配的数组（使用`malloc`、`calloc`或`realloc`），则数组的内存是在堆上分配的。

```c
int *dynamicArray = malloc(10 * sizeof(int)); // 实际上这不是数组指针，但可以这样使用  
if (dynamicArray == NULL) {  
    // 错误处理  
}  
  
// 如果你想有一个真正的数组指针（指向数组的指针），你需要这样：  
int (*arrayPtr)[10] = malloc(sizeof(int[10])); // 分配一个包含10个int的数组的内存  
if (arrayPtr == NULL) {  
    // 错误处理  
}
```

注意：在上面的`malloc`例子中，`dynamicArray`实际上是一个指向`int`的指针，而不是指向数组的指针。但我们可以像操作数组一样通过它来访问内存。然而，为了有一个真正的数组指针（即指向数组的指针），我们需要像第二个`malloc`调用那样分配内存。

(3) **数组作为结构体成员**：
如果数组是指针指向的结构体中的一个成员，并且该结构体实例是在栈上分配的，那么数组（如果它是结构体的一个固定大小的数组成员）将在栈上分配。如果数组是指针，并且该指针指向动态分配的内存，则数组的内存将在堆上分配。

```c
typedef struct {  
    int array[10]; // 固定大小的数组在栈上（如果结构体实例在栈上）  
} StructWithArray;  
  
typedef struct {  
    int *arrayPtr; // 指针在栈上，但它指向的内存可能在堆上或其他地方  
} StructWithArrayPtr;  
  
// 使用示例：  
StructWithArray stackStruct; // array在栈上  
StructWithArrayPtr *heapStructPtr = malloc(sizeof(StructWithArrayPtr)); // 结构体实例在堆上，但arrayPtr本身在堆上的这个结构体实例中  
heapStructPtr->arrayPtr = malloc(10 * sizeof(int)); // 数组的内存在堆上
```

总结：数组指针本身（即存储指针变量的内存）的位置取决于它是如何声明的（局部变量、全局变量等），但它所指向的内存区域的位置取决于你如何分配它（静态分配、动态分配等）。