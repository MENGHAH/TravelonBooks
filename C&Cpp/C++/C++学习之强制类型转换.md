# 强制类型转换运算符

带着三个疑问阅读：

1. 出现的背景是什么？
2. 何时使用？
3. 如何使用？

> [MSDN . 强制转换运算符](https://msdn.microsoft.com/zh-CN/library/5f6c9f8h.aspx)
> [C++中的四种强制类型转换符详解](https://www.cnblogs.com/linuxAndMcu/p/10387829.html)

## 1. static_cast

**(1) 使用场景**

- 在基本数据类型之间转换，如把 int 转换为 char，这种带来安全性问题由程序员来保证；
- 在有类型指针与 void * 之间转换；
- 用于类层次结构中基类和派生类之间指针或引用的转换。
  - 上行转换（派生类---->基类）是安全的；
  - 下行转换（基类---->派生类）由于没有动态类型检查，所以是不安全的。

**(2) 使用特点**

- 主要执行非多态的转换操作，用于代替C中通常的转换操作。
- 隐式转换都建议使用 static_cast 进行标明和替换。
- 不能使用 static_cast 在有类型指针内转换。

## 2. dynamic_cast

**(1) 使用场景**

- 用于将一个父类的指针/引用转化为子类的指针/引用（下行转换）

**(2) 使用特点**

- 基类必须要有虚函数，因为 dynamic_cast 是运行时类型检查，需要运行时类型信息，而这个信息是存储在类的虚函数表中。
- 对于下行转换，dynamic_cast 是安全的（当类型不一致时，转换过来的是空指针），而 static_cast 是不安全的。
- 对指针进行 dynamic_cast，失败返回 NULL，成功返回正常 cast 后的对象指针；对引用进行 dynamic_cast，失败抛出一个异常，成功返回正常 cast 后的对象引用。

### 2.1 dynamic_cast使用注意事项

`dynamic_cast`是C++中的一个类型转换运算符，主要用于类层次间的上下行转换。它提供了运行时类型检查的功能，即只有在转换是安全的情况下，才进行转换。使用`dynamic_cast`时，你需要注意以下几点：

1. **只能用于多态类型**：`dynamic_cast`主要用于处理继承体系中的类对象，特别是那些有虚函数的类（即多态类型）。对于非多态类型，`dynamic_cast`的结果将是未定义的。
2. **运行时类型信息（RTTI）**：`dynamic_cast`依赖于RTTI来工作。如果你的编译器禁用了RTTI（例如，通过编译器选项禁用），那么`dynamic_cast`将无法正常工作。
3. **转换失败的处理**：如果转换失败（例如，尝试将基类对象转换为派生类对象，而该基类对象实际上并不是该派生类的实例），`dynamic_cast`将返回空指针（对于指针类型）或抛出异常（对于引用类型）。因此，你需要确保对`dynamic_cast`的结果进行适当的检查。
4. **性能问题**：由于`dynamic_cast`在运行时进行类型检查，因此它可能比静态类型转换（如`static_cast`）慢。在性能关键的部分，应尽量避免不必要的`dynamic_cast`。
5. **避免滥用**：虽然`dynamic_cast`提供了强大的功能，但过度使用它可能会使代码难以理解和维护。在可能的情况下，应尽量使用其他设计策略（如接口、模板等）来实现所需的功能。
6. **交叉转换**：`dynamic_cast`**不支持交叉转换**，即如果类B和类C都继承自类A，那么你不能使用`dynamic_cast`将类B的对象转换为类C的对象（或反之）。

总的来说，虽然`dynamic_cast`是一个强大的工具，但在使用时需要谨慎，并确保理解其工作原理和限制。

## 3. const_cast

(1) 使用场景

- 常量指针（或引用）与非常量指针（或引用）之间的转换。

(2) 使用特点

- cosnt_cast 是四种类型转换符中唯一可以对常量进行操作的转换符。
- 去除常量性是一个危险的动作，尽量避免使用。

## 4. reinterpret_cast

(1) 使用场景

- 用在任意指针（或引用）类型之间的转换。
- 能够将整型转换为指针，也可以把指针转换为整型或数组。

(2) 使用特点

- reinterpret_cast 是从底层对数据进行重新解释，依赖具体的平台，可移植性差。不到万不得已，不用使用这个转换符，高危操作

## 5. 实例程序

```c++
#include <iostream>

namespace staticCast{
/**
 * @brief static_cast的使用场景
 * 1. 使用static_cast在基本数据类型之间转换
 * 2. 使用static_cast在有类型指针与void *之间转换
 * 3. 用于类层次结构中基类和派生类之间指针或引用的转换
 */

class CBase // 基类(父类)
{
    
};

class CDerived : public CBase  // 派生类(子类)
{

};

int useof_static_cast()
{
	// 1. 使用static_cast在基本数据类型之间转换
	float fval = 10.12;
	int ival = static_cast<int>(fval);  // float --> int
	std::cout << ival << std::endl;  // out: 10

	// 2. 使用static_cast在有类型指针与void *之间转换
	int *intp = &ival;
	void *voidp = static_cast<void *>(intp); // int* --> void*
	// cout << *voidp << endl; // error,voidp的大小未知
	long *longp = static_cast<long *>(voidp);
	std::cout << *longp << std::endl; // out: 10

	// 3. 用于类层次结构中基类和派生类之间指针或引用的转换
	// 上行转换（派生类---->基类）是安全的
	CDerived *tCDerived1 = nullptr;
	CBase *tCBase1 = static_cast<CBase*>(tCDerived1);
	// 下行转换（基类---- > 派生类）由于没有动态类型检查，所以是不安全的
	CBase *tCBase2 = nullptr;
	CDerived *tCDerived2 = static_cast<CDerived*>(tCBase2); //不会报错，但是不安全

	// 4. 不能使用static_cast在有类型指针内转换
	float *floatp = &fval;  //10.12的addr
	//int *intp1 = static_cast<int *>(floatp); // error,不能使用static_cast在有类型指针内转换
	std::cout << *floatp << std::endl;    // out: 10.12
}
}// namespace staticCast


namespace dynamicCast{

/**
 * @brief dynamic_cast使用
 * 1. 用于将一个父类的指针/引用转化为子类的指针/引用（下行转换）
 * 2. 当某个成员函数只在子类中存在，父类指针指向子类对象。此时该指针想要使用子类的成员函数，需要将父类指针转换为子类指针
 */
class CBase    // 基类(父类)
{
public:
	// dynamic_cast在将父类cast到子类时，父类必须要有虚函数
	virtual int test() { return 0; } // 一定要是 virtual
};

class CDerived : public CBase  // 派生类(子类)
{
public:
	int test() { return 1; }
};

int useof_dynamic_cast()
{
	CBase *p_CBase = new CBase;  // 基类对象指针
	CDerived *p_CDerived = dynamic_cast<CDerived *>(p_CBase);  // 将基类对象指针类型转换为派生类对象指针

	CBase i_CBase;    // 创建基类对象
	CBase &r_CBase = i_CBase;    // 基类对象的引用
	CDerived &r_CDerived = dynamic_cast<CDerived &>(r_CBase);  // 将基类对象的引用转换派生类对象的引用
}
} //namespace dynamicCast

namespace constCast
{
/**
 * @brief const_cast的使用
 * 
 *
 */
int useof_const_cast()
{
	int value = 100;
	const int *cpi = &value; // 定义一个常量指针
	//*cpi = 200;   // 不能通过常量指针修改值

	// 1. 将常量指针转换为非常量指针,然后可以修改常量指针指向变量的值
	int *pi = const_cast<int *>(cpi);
	*pi = 200;

	// 2. 将非常量指针转换为常量指针
	const int *cpi2 = const_cast<const int *>(pi); // *cpi2 = 300;  //已经是常量指针

	const int value1 = 500;
	const int &c_value1 = value1; // 定义一个常量引用

	// 3. 将常量引用转换为非常量引用
	int &r_value1 = const_cast<int &>(c_value1);

	// 4. 将非常量引用转换为常量引用
	const int &c_value2 = const_cast<const int &>(r_value1);
}  
} // namespace constCast


namespace reinterpretCast
{
int useof_reinterpret_cast()
{
	int value = 100;
	// 1. 用在任意指针（或引用）类型之间的转换
	double *pd = reinterpret_cast<double *>(&value);
	std::cout << "*pd = " << *pd << std::endl;

	// 2. reinterpret_cast能够将指针值转化为整形值
	int *pv = &value;
	int pvaddr = reinterpret_cast<int>(pv);
	std::cout << "pvaddr = " << pvaddr << std::endl;
	std::cout << "pv = " << pv << std::endl;
}

    
} // namespaceCast

int main(int argc, char* argv[])
{
    staticCast::useof_static_cast();
    dynamicCast::useof_dynamic_cast();
    constCast::useof_const_cast();
    reinterpretCast::useof_reinterpret_cast();
}