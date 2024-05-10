## 1. 对象作为map、unorder_map的key

> [c++中以类对象作为key用于unordered_map、map，以及std::tie技巧使用](https://blog.csdn.net/qq_42604176/article/details/129999237)

如果想让类对象作为std::unordered_map的key的话需要添加两个条件：

- operator==，用于查找
- 实现对应的hash类

```cpp
#include <iostream>
#include <unordered_map>
// 用于表示用户区域信息
class UserRegion {
public:
    int country_id;
    int province_id;
    int isp_id;

    bool operator==(const UserRegion& other) const {
        return country_id == other.country_id && province_id == other.province_id &&
               isp_id == other.isp_id;
    }
};

struct HashUserRegion {
    std::size_t operator()(const UserRegion& ur) const {
        std::size_t h1 = std::hash<int>{}(ur.country_id);
        std::size_t h2 = std::hash<int>{}(ur.province_id);
        std::size_t h3 = std::hash<int>{}(ur.isp_id);
        return h1 ^ (h2 << 1) ^ (h3 << 2);
    }
};

int main() {
    std::unordered_map<UserRegion, std::string, HashUserRegion> user_region_map = {
            {{1, 1, 1}, "user1"},
            {{1, 2, 1}, "user2"},
            {{2, 1, 2}, "user3"}
    };
    UserRegion test = {1, 1, 1};
    if (user_region_map.find(test) != user_region_map.end()) {
        std::cout << user_region_map[test] << std::endl;
    }
    // 遍历 unordered_map
    for (auto it = user_region_map.begin(); it != user_region_map.end(); ++it) {
        const UserRegion &ur = it->first;
        const std::string &user_name = it->second;
        std::cout << user_name << std::endl;
    }
    return 0;
}
```

如果想让想让类对象作为std::map的key的话需要添加一个条件：

- operator<，用于查找

## 2. new和delete底层执行逻辑

> [C++ new操作符详解](https://www.cnblogs.com/shijingjing07/p/5519153.html)

### 2.1 new

**new是一种操作符**。我们通常讲的new是指的是new operator，其实还有另外两个概念，operator new 和 placement new。

**(1) new operator**     

我们在使用new operator的时候，**实际上是执行了三个步骤:**

**1）调用operator new分配内存 ；2）调用构造函数生成类对象；3）返回相应指针。**

**(2) operator new**

operator new做的事情是new operator的一部分。operator new的原型如下：

```cpp
Void* operator new(size_t size);
```

参数size指定待分配的内存大小，函数内部调用malloc初始化内存，返回指向这个内存的指针。operator new默认情况下首先调用分配内存的代码，尝试得到一段堆上的空间，如果成功就返回，如果失败，则转而去调用一个new_hander(异常处理函数)，若没有定义new_hander，则抛出异常，否则执行new_hander，然后继续重复前面过程。该函数可以重载，在重载的时候可以加上额外的参数，但是第一个参数类型必须是size_t。

**(3) placement new**

placement new是用来实现定位构造的，因此可以实现new operator三步操作中的第二步。其实它也只是operator new的一个重载的版本，只是我们很少用到它。如果你想在已经分配的内存中创建一个对象，使用new时行不通的。也就是说placement new允许你在一个已经分配好的内存中（栈或者堆中）构造一个新的对象。原型中void*p实际上就是指向一个已经分配好的内存缓冲区的的首地址。

## 3. 多继承和多重继承

## 3.1 多重继承

## 3.2 多继承



### 3.3 交叉继承中构造函数和析构函数调用顺序

```cpp
class A{
public:
    A(){std::cout << "A Constructor" << std::endl;}
    ~A(){std::cout << "release A" << std::endl;}
};

class C{
public:
    C(){std::cout << "C Constructor" << std::endl;}
    ~C(){std::cout << "release C" << std::endl;}
};

class B : public A{
public:
    B(){std::cout << "B Constructor" << std::endl;}
    ~B(){std::cout << "release B" << std::endl;}

private:
    C c;
};
int main(){
    B b;
}
```

上述调用的输出如下：

```
A Constructor
C Constructor
B Constructor
release B
release C
release A
```

## 4. 强制类型转换

<font color=red>**static_cast**</font>

**用法：static_cast <类型说明符> （变量或表达式）**

**它主要有如下几种用法：**
  （1）用于类层次结构中基类和派生类之间指针或引用的转换
   进行上行转换（把派生类的指针或引用转换成基类表示）是安全的
   进行下行转换（把基类的指针或引用转换为派生类表示），由于没有动态类型检查，所以是**不安全**的
  （2）用于基本数据类型之间的转换，如把int转换成char。这种转换的安全也要开发人员来保证
  （3）把空指针转换成目标类型的空指针
  （4）把任何类型的表达式转换为void类型
  注意：static_cast不能转换掉expression的const、volitale或者__unaligned属性。

### 4.1 使用dynamic_cast需要注意什么

`dynamic_cast`是C++中的一个类型转换运算符，主要用于类层次间的上下行转换。它提供了运行时类型检查的功能，即只有在转换是安全的情况下，才进行转换。使用`dynamic_cast`时，你需要注意以下几点：

1. **只能用于多态类型**：`dynamic_cast`主要用于处理继承体系中的类对象，特别是那些有虚函数的类（即多态类型）。对于非多态类型，`dynamic_cast`的结果将是未定义的。
2. **运行时类型信息（RTTI）**：`dynamic_cast`依赖于RTTI来工作。如果你的编译器禁用了RTTI（例如，通过编译器选项禁用），那么`dynamic_cast`将无法正常工作。
3. **转换失败的处理**：如果转换失败（例如，尝试将基类对象转换为派生类对象，而该基类对象实际上并不是该派生类的实例），`dynamic_cast`将返回空指针（对于指针类型）或抛出异常（对于引用类型）。因此，你需要确保对`dynamic_cast`的结果进行适当的检查。
4. **性能问题**：由于`dynamic_cast`在运行时进行类型检查，因此它可能比静态类型转换（如`static_cast`）慢。在性能关键的部分，应尽量避免不必要的`dynamic_cast`。
5. **避免滥用**：虽然`dynamic_cast`提供了强大的功能，但过度使用它可能会使代码难以理解和维护。在可能的情况下，应尽量使用其他设计策略（如接口、模板等）来实现所需的功能。
6. **交叉转换**：`dynamic_cast`**不支持交叉转换**，即如果类B和类C都继承自类A，那么你不能使用`dynamic_cast`将类B的对象转换为类C的对象（或反之）。

总的来说，虽然`dynamic_cast`是一个强大的工具，但在使用时需要谨慎，并确保理解其工作原理和限制。

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
    Complex<int> a(10,20);  //对象的定义，必须声明模板类型，因为要分配内容
    Complex<int> b(20,30);
    Complex<int> c = a + b;   
    return 0;
}
```

### 5.1 类模板继承需要注意点

- 类模板派生类模板：从父类模板继承过来的成员函数无法直接使用，需要通过this指针、类名直接调用、或者using指明等方法使用

- 类模板派生普通类：如果子类不是模板类，需要指明父类的具体类型

## 6. 纯虚函数

纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加 **=0**:

```cpp
virtual void funtion1()=0
```

基类中定义的纯虚函数**在派生出的子类中必须实现**，否则编译会报错。基类中定义纯虚函数主要由于以下两个原因：

- 为了方便使用多态特性，我们常常需要在基类中定义虚拟函数。
- 在很多情况下，基类本身生成对象是不合情理的。例如，动物作为一个基类可以派生出老虎、孔雀等子类，但动物本身生成对象明显不合常理。

## 7. =default 和 =delete

### 7.1 =delete

- 用于显式地删除一个成员函数的定义。
- 当类中的某个成员函数被声明为 =delete 时,编译器将不会为该函数生成定义,也不允许在程序中调用该函数。
- 这通常用于禁止特定的操作,例如禁止拷贝构造函数或赋值操作符。

```cpp
class MyClass {
public:
    MyClass(int i) { /* ... */ }
    MyClass(const MyClass&) = delete; // 禁止拷贝构造函数
    MyClass& operator=(const MyClass&) = delete; // 禁止赋值操作符
};
```

### 7.2 =default

- 用于要求编译器自动生成一个成员函数的默认实现。
- 当类中的某个成员函数被声明为 =default 时,编译器将为该函数生成一个合适的默认实现,而不需要程序员手动编写。
- 这通常用于恢复编译器自动生成的特殊成员函数,例如默认构造函数、拷贝构造函数、赋值操作符等。

```cpp
class MyClass {
public:
    MyClass() = default; // 使用编译器生成的默认构造函数
    MyClass(const MyClass&) = default; // 使用编译器生成的默认拷贝构造函数
    MyClass& operator=(const MyClass&) = default; // 使用编译器生成的默认赋值操作符
};

```

## 8. 右值引用

**(1)右值是什么?**

- 在 C++ 中,所有的表达式都有一个左值(lvalue)和一个右值(rvalue)。
- 左值代表一个可以被赋值的对象,它有一个确定的内存地址。
- 而右值代表一个临时的、无法被直接赋值的值。

**(2) 右值引用是什么?**
右值引用使用 && 来声明,它允许绑定到一个右值。通过使用右值引用,可以避免不必要的复制操作,从而提高程序的性能。

**(3) 右值引用的使用场景**

- 移动语义(Move Semantics)：可以通过右值引用实现移动语义,将资源从一个对象转移到另一个对象,而不是进行深拷贝。这对于大对象来说可以带来显著的性能提升。
- 完美转发(Perfect Forwarding)：可以使用右值引用来实现完美转发,确保参数以原始类型被传递到函数中。这对于泛型编程很有帮助。
- 自定义类型的优化：对于自定义的类型,你可以利用右值引用来实现针对性的优化,比如资源管理、内存分配等。
