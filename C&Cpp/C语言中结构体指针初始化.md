# 结构体中成员指针变量的初始化

```c
#include <stdio.h>   
#include <stdlib.h>   
#include <string.h>   
  
struct student{   
  char *name;   
  int score;   
  struct student* next;   
}stu,*stu1;    
  
int main(){    
  stu.name = (char*)malloc(sizeof(char)); /*1.结构体成员指针需要初始化*/  
  strcpy(stu.name,"Jimy");   
  stu.score = 99;   
  
  stu1 = (struct student*)malloc(sizeof(struct student));/*2.结构体指针需要初始化*/  
  stu1->name = (char*)malloc(sizeof(char));/*3.结构体指针的成员指针同样需要初始化*/  
  
  stu.next  = stu1;   
  strcpy(stu1->name,"Lucy");   
  stu1->score = 98;   
  stu1->next = NULL;   
    
  printf("name %s, score %d /n ",stu.name, stu.score);   
  printf("name %s, score %d /n ",stu1->name, stu1->score);   
  free(stu1);   
  return 0;   
}  
```



# 参考内容

[C语言--结构体成员指针变量与结构体指针变量的区别](https://blog.csdn.net/faihung/article/details/90679893)