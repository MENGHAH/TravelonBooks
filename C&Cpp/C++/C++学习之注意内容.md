# C++编程中易错点

## 1. 对象作为map、unorder_map的key

> [c++中以类对象作为key用于unordered_map、map，以及std::tie技巧使用](https://blog.csdn.net/qq_42604176/article/details/129999237)

如果想让类对象作为std::unordered_map的key的话需要添加两个条件：

- operator==，用于查找
- 实现对应的hash类

如果想让想让类对象作为std::map的key的话需要添加一个条件：

- operator<，用于查找

## 2. new和delete底层执行逻辑

> [C++ new操作符详解](https://www.cnblogs.com/shijingjing07/p/5519153.html)

### 2.1 new

**new是一种操作符**。我们通常讲的new是指的是new operator，其实还有另外两个概念，operator new 和 placement new。

**(1) new operator**     

我们在使用new operator的时候，**实际上是执行了三个步骤:**

1）调用operator new分配内存 ；2）调用构造函数生成类对象；3）返回相应指针。

**(2) operator new**

operator new做的事情是new operator的一部分。operator new的原型如下：

```cpp
Void* operator new(size_t size);
```

参数size指定待分配的内存大小，函数内部调用malloc初始化内存，返回指向这个内存的指针。operator new默认情况下首先调用分配内存的代码，尝试得到一段堆上的空间，如果成功就返回，如果失败，则转而去调用一个new_hander(异常处理函数)，若没有定义new_hander，则抛出异常，否则执行new_hander，然后继续重复前面过程。该函数可以重载，在重载的时候可以加上额外的参数，但是第一个参数类型必须是size_t。

**(3) placement new**

placement new是用来实现定位构造的，因此可以实现new operator三步操作中的第二步。其实它也只是operator new的一个重载的版本，只是我们很少用到它。如果你想在已经分配的内存中创建一个对象，使用new时行不通的。也就是说placement new允许你在一个已经分配好的内存中（栈或者堆中）构造一个新的对象。原型中void*p实际上就是指向一个已经分配好的内存缓冲区的的首地址。

## 3. 多继承



## 4. 强制类型转换





## 5. 类模板

类模板是一种泛型，其实现依赖于`template`和`typename` 。简单实现如下：

```cpp
template <typename T>
class Complex{
public:
    Complex(T a, T b){
        this->a = a;
        this->b = b;
    }
    Complex<T> operator+(Complex &c){
        Complex<T> tmp(this->a+c.a, this->b+c.b);
        return tmp;
    }     
private:
    T a;
    T b;
}

int main(){
    //对象的定义，必须声明模板类型，因为要分配内容
    Complex<int> a(10,20);  
    Complex<int> b(20,30);
    Complex<int> c = a + b;   
    return 0;
}
```





## 6. 纯虚函数

