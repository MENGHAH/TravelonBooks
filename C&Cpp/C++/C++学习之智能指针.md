# 1. 智能指针介绍

智能指针主要用于管理在堆上分配的内存，它将普通的指针封装为一个栈对象。当栈对象的生存周期结束后，会在析构函数中释放掉申请的内存，从而防止内存泄漏。C++ 11中最常用的智能指针类型为shared_ptr,它采用引用计数的方法，记录当前内存资源被多少个智能指针引用。该引用计数的内存在堆上分配。当新增一个时引用计数加1，当过期时引用计数减一。只有引用计数为0时，智能指针才会自动释放引用的内存资源。对shared_ptr进行初始化时不能将一个普通指针直接赋值给智能指针，因为一个是指针，一个是类。可以通过make_shared函数或者通过构造函数传入普通指针。并可以通过get函数获得普通指针。

# 2. 为什么使用智能指针

智能指针的作用是管理一个指针，因为存在以下这种情况：申请的空间在函数结束时忘记释放，造成内存泄漏。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源。所以智能指针的作用原理就是在函数结束时自动释放内存空间，不需要手动释放内存空间。

# 3. 智能指针分类

## 3.1 unique_ptr

> 使用原则：如果不是很明确是否要用shared_ptr，那么就使用unique_ptr

unique_ptr是独占指针，当初始化一个独占指针并指向一个对象时，那么该对象的所有权就只属于该指针，不可被复制。但是，对象的所有权可以从当前指针**移动**到其他指针。

## 3.2 shared_ptr

> [shared_ptr是线程安全的吗？](https://cloud.tencent.com/developer/article/1654442) 

> shared_ptr的引用计数本身是安全且无锁的，但是对象的读写则不是，因为shared_ptr有两个数据成员（原始指针和计数器），读写不能原子化。

shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“最后一个引用被销毁”时候释放。该指针指向的资源可以被多个指针共享，它使用计数机制来表明资源被几个指针共享。

## 3.3 weak_ptr

weak_ptr是为了配合shared_ptr而引入的一种智能指针，它指向一个由shared_ptr管理的对象而不影响所指对象的生命周期，**也就是将一个weak_ptr绑定到一个shared_ptr不会改变shared_ptr的引用计数**。不论是否有weak_ptr指向，一旦最后一个指向对象的shared_ptr被销毁，对象就会被释放。从这个角度看，weak_ptr更像是shared_ptr的一个助手而不是智能指针。

**(1) 如何使用weak_ptr**

weak_ptr并没有重载operator->和operator *操作符，因此不可直接通过weak_ptr使用对象，典型的用法是调用其lock函数来获得shared_ptr实例，进而访问原始对象。

**(2)如何判断weak_ptr指向对象是否存在**

> weak_ptr可以提升为shared_ptr

既然weak_ptr并不改变其所共享的shared_ptr实例的引用计数，那就可能存在weak_ptr指向的对象被释放掉这种情况。这时，就不能使用weak_ptr直接访问对象。那么如何判断weak_ptr指向对象是否存在？C++中提供了lock函数来实现该功能。如果对象存在，lock()函数返回一个指向共享对象的shared_ptr，否则返回一个空shared_ptr。

```C++
class A{
public:
    A() : a(3) { cout << "A Constructor..." << endl; }
    ~A() { cout << "A Destructor..." << endl; }
    int a;
};

int main() {
    shared_ptr<A> sp(new A());
    weak_ptr<A> wp(sp);
    //sp.reset();
    if (shared_ptr<A> pa = wp.lock()){
        cout << pa->a << endl;
    }else{
        cout << "wp指向对象为空" << endl;
    }
}
```

# Q 其他问题

## Q1. shared_ptr是线程安全的吗？

> [c++11总结15——shared_ptr在多线程下的安全性问题](https://blog.csdn.net/www_dong/article/details/114418454)
>
> https://www.zhihu.com/question/56836057

**boost官方文档中有如下结论：**

- 同一个shared_ptr被多个线程“读”是安全的；
- 同一个shared_ptr被多个线程“写”是不安全的；
- 共享引用计数的不同的shared_ptr被多个线程”写“ 是安全的；

shared_ptr指针类有两个成员变量，一个是指向变量的指针；一个是资源被引用的次数，引用次数加减操作内部自动加锁解锁，是线程安全的。

**(1) 引用计数**
虽然引用计数存在于每一个shared_ptr对象中，但是实际上它是要跟随对象所管理的资源。引用计数会随着指向这块资源的shared_ptr对象的增加而增加。因此引用计数是要指向同一块资源的所有的对象共享的，所以实际上引用计数在shared_ptr底层中是以指针的形式实现的，所有的对象通过指针访问同一块空间，从而实现共享。

那么也就是说，引用计数是一个临界资源，所以在多线程中，我们必须要保证临界资源访问的安全性，因此在shared_ptr底层中在对引用计数进行访问之前，首先对其加锁，当访问完毕之后，在对其进行解锁。所以shared_ptr的引用计数是线程安全的。

**(2) 被shared_ptr对象所管理的资源**
shared_ptr对象所管理的资源存放在堆上，它可以由多个shared_ptr所访问，所以这也是一个临界资源。因此当多个线程访问它时，会出现线程安全的问题。
**(3) 一个例子**

见第一个参考文章

##  Q2. 如何解决shared_ptr相互引用问题

基于weak_ptr的智能指针提升来判断对象是否已经析构，进而解决相互引用问题

```cpp
#include<iostream>
#include <memory>
#include <vector>
using namespace std;

class B;
class A {
public:
    A(){
        cout << "A::A()" << endl;
    }
 
    ~A(){
        cout << "A::~A()" << endl;
    }
 
    void setbPtr(shared_ptr<B> p){
        _bPtr = p;
    }

   void print_name(){
      cout << "Iam A" << endl;
   }
 
private:
    weak_ptr<B> _bPtr;
};
 
 
class B {
public:
    B(){
        cout << "B::B()" << endl;
    }
 
    ~B(){
        cout << "B::~B()" << endl;
    }
 
    void setaPtr(shared_ptr<A> p){
        _aPtr = p;
    }
 
private:
    weak_ptr<A> _aPtr; // 解决循环引用问题
};

class C
{
public:
   void push_back_a(std::shared_ptr<A> &a){
      a_vec.push_back(a);
   }

   void iterate_over_item(){
      std::vector<std::weak_ptr<A>>::iterator iter= a_vec.begin();
      for(; iter != a_vec.end(); ++iter){
         shared_ptr<A> iterm_a(iter->lock()); // 做弱指针的强制拉升，但是引用计数加一
         cout << "use_count of iterm_a: " << iterm_a.use_count() << endl;
         if(iterm_a){
            iterm_a->print_name();
         }
         // 使用结束后引用计数减一
      }
   }

private:
   std::vector<std::weak_ptr<A>> a_vec; // 如果此处使用shared_ptr会把入队对象的引用计数加一
   
};
 
int main() {
    shared_ptr<A> a(new A);
    shared_ptr<B> b(new B);
    shared_ptr<C> c(new C);

    cout << a.use_count() << endl;
    cout << b.use_count() << endl;
 
    a->setbPtr(b);
    b->setaPtr(a);

    cout << a.use_count() << endl;
    cout << b.use_count() << endl;
    cout << "********************" << endl;
    shared_ptr<A> a_0(new A);
    shared_ptr<A> a_1(new A);

    c->push_back_a(a_0);
    c->push_back_a(a_1);
    c->iterate_over_item();
    cout << a_0.use_count() << endl;
    cout << a_1.use_count() << endl;

    return 0;
}
```



# 参考文档

[C++ 智能指针的正确使用方式](https://www.cyhone.com/articles/right-way-to-use-cpp-smart-pointer/)

