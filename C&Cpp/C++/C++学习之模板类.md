## 1. 类模板概念

C++ 类模板是一种特殊的语法结构,用于定义一个通用的类,它可以适用于不同的数据类型，以增强C++泛型编程能力。类模板的主要特点如下:

- 模板参数化: 类模板使用模板参数来定义类型。这使得类模板可以处理各种不同的数据类型,而不需要为每种类型编写单独的类定义。

- 编译时泛型: 类模板是在编译时进行泛型编程,编译器会根据实际使用的类型生成相应的代码。这提高了代码的复用性和灵活性。

- 代码重用: 使用类模板,可以避免为每种数据类型编写重复的代码,提高了代码的重用性。

- 强类型检查: 类模板在编译时就会进行类型检查,这可以帮助开发者发现潜在的类型错误。

常见的类模板示例包括 std::vector、std::list、std::map 等,它们都是 C++ 标准库中定义的泛型容器类。通过使用模板参数,这些容器可以处理各种不同的数据类型。C++ 类模板为开发者提供了灵活、可重用的编程方式,增强了 C++ 语言的泛型编程能力。



<font color=red> **在定义对象时，必须声明模板类型，因为要分配内容**</font>

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

## 2. 类模板中的继承行为

举例1: 

```cpp
#include <iostream>

// 定义基类模板
template <typename T>
class BaseClass {
public:
    void baseFunction() {
        std::cout << "Executing BaseClass::baseFunction() with type: " << typeid(T).name() << std::endl;
    }

    template <typename U>
    void templateFunction() {
        std::cout << "Executing BaseClass::templateFunction() with types: " << typeid(T).name() << ", " << typeid(U).name() << std::endl;
    }
};

// 定义派生类模板
template <typename T, typename U>
class DerivedClass : public BaseClass<T> {
public:
    void derivedFunction() {
        std::cout << "Executing DerivedClass::derivedFunction() with types: " << typeid(T).name() << ", " << typeid(U).name() << std::endl;
    }

    // 重写基类的模板函数
    template <typename V>
    void templateFunction() {
        std::cout << "Executing DerivedClass::templateFunction() with types: " 
            	  << typeid(T).name() << ", " << typeid(U).name() << ", "
                  << typeid(V).name() << std::endl;
        BaseClass<T>::templateFunction<V>(); // 调用基类的模板函数
    }
};

int main() {
    // 创建 BaseClass 对象
    BaseClass<int> baseObj;
    baseObj.baseFunction();
    baseObj.templateFunction<double>();

    // 创建 DerivedClass 对象
    DerivedClass<int, double> derivedObj;
    derivedObj.baseFunction(); // 继承自基类的函数
    derivedObj.derivedFunction();
    derivedObj.templateFunction<char>(); // 重写的模板函数

    return 0;
}
```

举例2：

```cpp
/* c++类模板 继承 详解 */

/* 类模板和类模板之间、类模板和类之间可以互相继承。它们之间的派生关系有以下四种情况 */
/* 1)、类模板继承类模板 
   2)、类模板继承模板类
   3)、类模板继承普通类
   4)、普通类继承模板类*/

#include <iostream>
using namespace std;

//1、类模板继承类模板
template <typename T1, typename T2>
class A
{
    T1 x;
    T2 y;
};

template <typename T1, typename T2>
class B : public A<T2, T1>
{
    T1 x1;
    T2 y2;
};

template <typename T>
class C : public B<T, T>
{
    T x3;
};

//2、类模板继承模板类
template <typename T>
class D : public A<int, double> //具体化的模板类
{
    T x4;
};

//3、类模板继承普通类
class E
{
    int x4;
};
template <typename T>
class F : public E
{
    T X5;
};

//4、普通类继承模板类
template <typename T>
class G
{
    G g;
};
class H : public F<int>
{
    int h;
};
int main()
{
    //1、类模板继承类模板
    C<int> c;         //由派生的具体类型反推 基类 模板类型 C<int> B<int, int> A<int, int>
    B<int, char *> b; //由派生的具体类型反推 基类 模板类型 B<int, char*>, A<char*, int>

    //2、类模板继承模板类
    D<float> d; //生成D<float> 和 A<int, double> 模板类

    //3、类模板继承普通类
    F<bool> f; //生成 F<bool>

    //4、普通类继承模板类
    H g; //生成 F<int> 模板类
    return 0;
}

```

### 2.1 类模板派生类模板

（1）从父类模板继承过来的成员函数无法直接使用，需要通过this指针、类名直接调用、或者using指明等方法使用

```cpp
// templateInheritance2.cpp
#include <iostream>

template <typename T>
class Base{ // 父类
public:
  void func1() const{
    std::cout << "func1()\n";
  }
  void func2() const{
    std::cout << "func2()\n";
  }
  void func3() const{
    std::cout << "func3()\n";
  }
};

template <typename T>
class Derived: public Base<T> // 派生类
{
public:
  using Base<T>::func2;              // 方法（2）
  void callAllBaseFunctions(){
    this->func1();                   // 方法（1）
    func2();                         // 方法（2）
    Base<T>::func3();                // 方法（3）
  }
};

int main(){
  Derived<int> derived;
  derived.callAllBaseFunctions();
}
```



### 2.2 类模板派生普通类

（1）如果子类不是模板类，需要指明父类的具体类型

  ```cpp
  template <typename T>
  class Parent{
  public:
      Parent(T p)
      {
          this->p = p;
      }
      
  private:
      T p;
  };
  
  //如果子类不是模板类，需要指明父类的具体类型
  class ChildOne:public Parent<int>{
      
  public:
      ChildOne(int a,int b):Parent(b)
      {
          this->cone = a;
      }
      
  private:
      int cone;
  };
  ```

  

  