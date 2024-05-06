当结构体中存在指针成员时，你需要分别处理结构体的内存分配和指针成员所指向的内存分配。以下是一个示例，展示了如何初始化一个包含指针成员的结构体，并为其指针成员分配内存：

```c
#include <stdio.h>  
#include <stdlib.h>  
#include <string.h>  
  
// 定义一个结构体  
typedef struct {  
    int id;  
    char *name; // 指针成员  
    float score;  
} Student;  
  
int main() {  
    // 1. 分配内存给结构体实例  
    Student *ptr_student = (Student*)malloc(sizeof(Student));  
      
    // 检查内存是否成功分配  
    if (ptr_student == NULL) {  
        perror("Memory allocation for Student failed");  
        return EXIT_FAILURE;  
    }  
      
    // 假设我们要为name分配50个字符的空间  
    // 2. 分配内存给指针成员name  
    ptr_student->name = (char*)malloc(50 * sizeof(char));  
      
    // 检查内存是否成功分配  
    if (ptr_student->name == NULL) {  
        perror("Memory allocation for name failed");  
        // 释放已经为结构体分配的内存  
        free(ptr_student);  
        return EXIT_FAILURE;  
    }  
      
    // 3. 通过指针初始化结构体的成员  
    ptr_student->id = 1;  
    strcpy(ptr_student->name, "Alice"); // 复制字符串到分配的内存中  
    ptr_student->score = 90.5f;  
      
    // 使用结构体中的数据  
    printf("ID: %d, Name: %s, Score: %.1f\n", ptr_student->id, ptr_student->name, ptr_student->score);  
      
    // 4. 释放内存  
    // 首先释放指针成员name所指向的内存  
    free(ptr_student->name);  
    // 然后释放结构体本身的内存  
    free(ptr_student);  
      
    return EXIT_SUCCESS;  
}
```

在这个示例中：

1. 我们首先定义了一个名为`Student`的结构体，它包含一个整数`id`、一个字符指针`name`和一个浮点数`score`。
2. 在`main`函数中，我们首先使用`malloc`为`Student`结构体实例分配内存，并检查是否成功。
3. 接着，我们为结构体中的指针成员`name`分配内存。这里我们假设`name`需要50个字符的空间，所以为其分配了50个字节的内存。同样，我们需要检查内存是否成功分配。
4. 然后，我们通过结构体指针`ptr_student`来访问和初始化结构体的成员，包括为`name`指针成员分配的内存。
5. 在使用完结构体后，我们需要先释放指针成员`name`所指向的内存，然后再释放结构体本身的内存。这是非常重要的，因为忘记释放内存会导致内存泄漏。

注意：在实际应用中，你可能还需要考虑错误处理、内存对齐、内存碎片等问题，以确保程序的健壮性和性能。