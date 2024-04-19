# 参考文档

[[C++ 多重继承](https://www.cnblogs.com/wkfvawl/p/10756539.html)](https://www.cnblogs.com/wkfvawl/p/10756539.html)

## 什么是多重继承

在现实生活中，一些新事物往往会拥有两个或者两个以上事物的属性，为了解决这个问题，C++引入了多重继承的概念，C++允许为一个派生类指定多个基类，这样的继承结构被称做多重继承。（派生类有两个或两个以上的直接基类）当一个派生类要使用多重继承的时候，必须在派生类名和冒号之后列出所有基类的类名，并用逗好分隔。

```c++
 class Derived : public Base1, public Base2, … {}；
```

存在多继承的类层次图是一个图(graph)。
只有单继承的类层次图是一个树(tree)。

## 多重继承的歧义性（菱形继承）

![img](https://img2018.cnblogs.com/blog/1358881/201904/1358881-20190423150958351-501743796.png)

**在类DC中有4个int类型的成员。**

<font color=red>问题1：类DC的对象中存在多个同名成员 x， 应如何使用？</font>

<font color=red>问题2：类DC的对象中，存在两份来自类BC0的成员K，如何区分？</font>

```cpp
int main( ){
    DC d;
    d.x = 1;       // error C2385: 对"x"的访问不明确
                   // 可能是"x"(位于基"BC1"中)，也可能是"x"(位于基"BC2"中)
    d.BC1::x = 2;  // OK，from BC1
    d.BC2::x = 3;  // OK，from BC2
    d.K = 4;       // error C2385: 对"K"的访问不明确
    d.BC1::K = 5;  // OK，from BC1
    d.BC2::K = 6;  // OK，from BC2
    return 0;
}
```

**解决方案：**
在BC1类和BC2类继承BC0时，其前面加上virtual关键字就可以实现<font color=red>**虚拟继承**</font>，使用虚拟继承后，**当系统碰到多重继承的时候就会先自动加一个BC0的拷贝，当再次请求一个BC0的拷贝时就会被忽略，以保证继承类成员函数的唯一性。**

```cpp
class BC0
{
public:
    int K;
};
class BC1 : virtual public BC0
{
public:
    int x;
};
class BC2 : virtual public BC0
{
public:
    int x;
};
class DC : public BC1, public BC2
{
};
void main( )
{
    DC d;          //虚继承使得BC0仅被DC间接继承一份
    d.K = 13;      // OK
  	// d.x = 10;   // error, 依旧存在歧义性
    d.BC0::X = 10； // OK
}
```

## 多重继承下的构造函数

单继承派生类构造时，首先构造基类，其次是派生类的数据成员的初始化（顺序和派生类数据成员的声明顺序相同），最后执行派生类的构造函数体。
多继承派生类构造时，首先构造虚基类，多个虚基类按照他们被继承的顺序依次构造，其次构造一般基类，多个一般基类按照被继承的顺序构造，然后初始化派生类的数据成员，初始化顺序和派生类数据成员的声明顺序相同，最后执行派生类的构造函数体。

```cpp
#include<iostream>
using namespace std;
class B1
{
public:
    B1(int i)
    {
        cout<<"B1"<<endl;
    }
};
class B2
{
public:
    B2(int i)
    {
        cout<<"B2"<<endl;
    }
};
class B3
{
public:
    B3(int i)
    {
        cout<<"B3"<<endl;
    }
};
class B4
{
public:
    B4(int i)
    {
        cout<<"B4"<<endl;
    }
};
class D : public B2, virtual B1, public B4, virtual B3
{
public:
    D(int n): B1(n), B2(n), B3(n), B4(n) {}
};
int main( )
{
    D d(1);
    return 0;
}
```

