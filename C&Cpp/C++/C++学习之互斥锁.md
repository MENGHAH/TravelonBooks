# 为什么需要锁

C++多线程编程需要对共享的数据进行写保护，以防止多线程在对共享数据成员进行并发写时造成资源争抢而导致出现崩溃或其他不符合预期的结果。通常的做法是在修改共享数据成员前先对互斥锁mutex进行lock加锁，在修改后再进行unlock操作，**这个场景中经常会出现由于疏忽或异常导致lock之后未能unlock，最终导致死锁**。

**针对以上的问题，C++11中引入了std::unique_lock与std::lock_guard两种数据结构**。通过对lock和unlock进行一次薄的封装，实现**自动unlock**的功能。

# 1. std::mutex

```c++
#include <iostream>
#include <mutex>
#include <thread>

#include <unistd.h>

using namespace std;

std::mutex mutex_;
int v_ = 1;

void criticalSection(std::string &threadName)
{
    cout << "threadName: " << threadName << endl;
    {
        std::lock_guard<std::mutex> lock(mutex_);
        v_++; //执行竞争操作
        std::cout << "v_: " << v_ << std::endl;
    } // 离开此作用域后锁会释放
}


std::mutex mymutex;
void sayHello()
{
    int k=0;
    std::unique_lock<mutex> lock(mymutex);
    while(k<2)
    {
        k++;
        cout<<endl<<"hello"<<endl;
        sleep(2);
    }
}
void sayWorld()
{
   std::unique_lock<mutex> lock(mymutex);
    while(1)
    {
         cout<<endl<<"world"<<endl;
         sleep(1);
    }
}

void testLockGuard()
{
    std::thread  t1(criticalSection, "thread1"), t2(criticalSection, "thread2");
    t1.join();
    t2.join();
}

void testUniqueLock()
{
    std::thread threadHello(&sayHello);
    std::thread threadWorld(&sayWorld);
    threadHello.join();
    threadWorld.join();
}

int main()
{
    testLockGuard();
    testUniqueLock();
}

/**
 * std::unique_lock和std::lock_guard的区别
 * 参考：https://blog.yanjingang.com/?p=6547
 * 
 * 如何选择使用哪种：lock_guard简单易用，unique_lock功能强大但管理成本略高。
 * 如果lock_guard能满足的场景尽量用lock_guard，否则可以考虑unique_lock。
 */

```

# 2. std::lock_guard

lock_guard是一个互斥量包装程序，它提供了一种方便的RALL（Resource accquisition is initialization）风格的机制来在作用域块的持续时间内拥有一个互斥量。

创建lock_guard对象时，它将尝试获取提供给他的互斥锁的所有权。当控制流离开lock_guard对象的作用域时，lock_guard析构并释放互斥量。

**特点：**

- 创建即加锁，作用域结束自动析构并解锁，无需手工解锁
- 不能中途解锁，必须等作用域结束才解锁
- 不能复制

```c++
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;     // 全局互斥锁
int gi = 0;         //多线程共享变量

void thread_mutex_lockguard(const int i){
    std::lock_guard<std::mutex> lock(mtx);  //持有互斥锁
    //std::unique_lock<std::mutex> lock(mtx); //持有互斥锁（可以用unique_lock替代lock_guard）
    gi += i;   //操作共享变量
    std::cout << "thread_mutex_lockguard! tid:[" << std::this_thread::get_id() << "] gi:" << gi << std::endl;
    // lock离开作用域时，自动释放互斥锁
}


int main(int argc, char* argv[]){
    // lock_guard 简易加锁(析构自释放)
    LOG(INFO) << "lock_guard:";
    std::thread thg[10];
    for(int i=0;i<10;i++){
        thg[i] = std::thread(thread_mutex_lockguard, i);
        thg[i].detach();
    }
    for(int i=0;i<10;i++){
        thg[i].join();
    }
    std::cout << "thread_mutex_lockguard gi:" << gi << std::endl;
}
```

# 3. std::unique_lock

unique_lock是一个通用的互斥量锁定包装器，它允许延迟锁定，限时深度锁定，递归锁定，锁定所有权的转移以及与condition variable条件变量一起使用。

简单的讲，unique_lock是lock_guard的升级版，它可以替代lock_guard，同时具备很多其他功能，可以应对更为复杂的锁需求。

**特点：**

- 创建时可以不加锁（指定第二参数为std::defer_lock），而在需要时再锁定
- 可以随时加锁解锁（代价是增加了锁状态的维护，空间和性能会略有影响）
- 作用域规则同lock_grard，析构时自动释放锁
- 不可复制，可移动（可通过move转到另一个scope中生存，增加了管理难度）
- condition variable条件变量需要该类型的锁作为参数（此场景必须使用unique_lock）

```c++
#include <mutex>
#include <thread>
#include <chrono>
#include <iostream>
#include <string>
using namespace std;
struct bank_account//银行账户
{
  explicit bank_account(string name, int money)
  {
    sName = name;
    iMoney = money;
  }

  string sName;
  int iMoney;
  mutex mMutex;//账户都有一个锁mutex
};
void transfer( bank_account &from, bank_account &to, int amount )//这里缺少一个from==to的条件判断个人觉得
{
  unique_lock<mutex> lock1( from.mMutex, defer_lock );//defer_lock表示延迟加锁，此处只管理mutex
  unique_lock<mutex> lock2( to.mMutex, defer_lock );
  lock( lock1, lock2 );//lock一次性锁住多个mutex防止deadlock
  from.iMoney -= amount;
  to.iMoney += amount;
  cout << "Transfer " << amount << " from "<< from.sName << " to " << to.sName << endl;
}
int main(){
  bank_account Account1( "User1", 100 );
  bank_account Account2( "User2", 50 );
  thread t1( [&](){ transfer( Account1, Account2, 10 ); } );//lambda表达式
  thread t2( [&](){ transfer( Account2, Account1, 5 ); } );
  t1.join();
  t2.join();
}
```

上述加锁流程也可以通过lock_guard来实现

```C++
// 先持有2个互斥锁
lock( from.mMutex, to.mMutex );
// 再将两个锁托管给2个lock_guard管理
lock_guard<mutex> lock1( from.mMutex, adopt_lock );//adopt_lock表示mutex已经上锁，lock1将拥有from.mMutex
lock_guard<mutex> lock2( to.mMutex, adopt_lock );
```

# 4. unique_lock配合条件变量的使用

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

// unique_lock + condition_variable条件锁
std::mutex mtx;     // 全局互斥锁
std::condition_variable cond; // 全局条件变量
bool ready = false; //测试阻塞condition_variable的条件

void thread_mutex_condition(const int i){
    std::cout << i << std::endl;
    std::unique_lock<std::mutex> lock(mtx);
    cond.wait(lock, []{ return ready; });
    //dosomthing
    std::cout << "thread_mutex_condition done: " << i << std::endl;
}

void thread_condition_ready(){
    std::cout << "thread_condition_ready!" << std::endl;
    std::unique_lock<std::mutex> lock(mtx);
    ready = true;   //使wait条件通过
    cond.notify_all();  //唤醒所有线程重新抢锁并重新判断wait条件
}


int main(int argc, char* argv[]){
    // unique_lock + condition_variable 锁启用条件变量
    std::cout << "unique_lock + condition_variable:" << std::endl;
    std::thread thm[10];
    for(int i=0;i<10;i++){
        thm[i] = std::thread(thread_mutex_condition, i);
        thm[i].detach();
    }
    thread_condition_ready();
}
```

# 5. 如何选择两种锁

lock_guard简单易用，unique_lock功能强大但管理成本略高。如果lock_guard能满足的场景尽量用lock_guard，否则可以考虑unique_lock。

# 参考文章

[C++ lock_guard和unique_lock的区别](https://blog.yanjingang.com/?p=6547)