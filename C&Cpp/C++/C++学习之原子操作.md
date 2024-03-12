# 1. 原子操作概述

所谓原子操作，就是多线程程序中“最小的且不可并行化的”操作。对于在多个线程间共享的一个资源而言，这意味着同一时刻，多个线程中有且仅有一个线程在对这个资源进行操作，即互斥访问。C++ 11 新增**atomic**可以实现原子操作。

**基于互斥锁也可以实现原子操作的功能，但是加锁和解锁的会影响程序运行效率**。因此，出现了原子操作。

# 2. C++11 中的原子操作

 标准原子类型的备选名和与其相关的`std::atomic<>`特化类对应如下表：

| 原子类型        | 相关特化类                      |
| :-------------- | :------------------------------ |
| atomic_bool     | std::atomic<bool>               |
| atomic_char     | std::atomic<char>               |
| atomic_schar    | std::atomic<signed char>        |
| atomic_uchar    | std::atomic<unsigned char>      |
| atomic_int      | std::atomic<int>                |
| atomic_uint     | std::atomic<unsigned>           |
| atomic_short    | std::atomic<short>              |
| atomic_ushort   | std::atomic<unsigned short>     |
| atomic_long     | std::atomic<long>               |
| atomic_ulong    | std::atomic<unsigned long>      |
| atomic_llong    | std::atomic<long long>          |
| atomic_ullong   | std::atomic<unsigned long long> |
| atomic_char16_t | std::atomic<char16_t>           |
| atomic_char32_t | std::atomic<char32_t>           |
| atomic_wchar_t  | std::atomic<wchar_t>            |

# 3. std::atomic使用

```c++
#include <atomic>
#include <thread>
#include <iostream>
using namespace std;

// atomic_int64_t total = 0;  // error 需要使用初始化列表初始化,否则编译报错
atomic_int64_t total(0); 			//atomic_int64_t相当于int64_t，但是本身就拥有原子性
// std::atomic<int> total(0);  // std::atomic<int> 和atomic_int64_t等效

//线程函数，用于累加
void threadFunc(int64_t endNum) {
	for (int64_t i = 1; i <= endNum; ++i) {
		total += i;
	}
}

int main() {
	int64_t endNum = 100;
	thread t1(threadFunc, endNum);
	thread t2(threadFunc, endNum);

	t1.join();
	t2.join();

	cout << "total=" << total << endl;    //10100
}
```

原子操作一般用于用户统计。例如统计一共发送了多少数据包，接收了多少个数据包。

