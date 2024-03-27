# std::promise

std::promise 是 C++11 标准库中提供的异步编程工具之一，它通常与 std::future 一起使用，**用来实现线程间的异步通信**。promise 提供了一个承诺（promise），表示在某个时间点一定会有一个值或一个异常被设置。promise 可以在一个线程中设置一个值，而另一个线程中可以通过 std::future 来访问这个值。通常的做法是，创建一个 promise 对象，然后通过 promise 对象获取一个 future 对象，将 future 对象传递到另一个线程中去，另一个线程将值或异常设置到 promise 对象中，随后原线程可以通过 future 对象来获取值或异常。

```c++
#include <vector>
#include <thread>
#include <future>
#include <numeric>
#include <iostream>
#include <chrono>

using namespace std::chrono::seconds;

// 异步求和
void Accumulate(std::vector<int>::iterator first,
    std::vector<int>::iterator last,
    std::promise<int> accumulate_promise)
{
    int sum = std::accumulate(first, last, 0);
    accumulate_promise.set_value(sum);  // Notify future
}
 
// 线程1设置值
void Do_work1(std::promise<int> &barrier)
{
    //为了突出效果，可以使线程休眠5s
    std::this_thread::sleep_for(seconds(3));
    int iVal = 233;
    std::cout << "传入数据(int): " << iVal << std::endl;
    barrier.set_value(iVal); // 设置共享状态的值, 此处和线程new_work_thread保持同步.
}
 
// 线程2获取值
void Do_work2(std::future<int> &fun)
{
    //阻塞函数，直到收到相关联的std::promise对象传入的数据
    auto iVal = fun.get();		//iVal = 233
    std::cout << "收到数据(int): " << iVal << std::endl;
}

int main()
{
    // 演示如何使用promise<int>在线程之间传输结果
    std::vector<int> numbers = { 1, 2, 3, 4, 5, 6 };
    std::promise<int> accumulate_promise; // 主线程生成一个 std::promise<int> 对象.
    std::future<int> accumulate_future = accumulate_promise.get_future(); // 和 future 关联.
    std::thread work_thread(Accumulate, numbers.begin(), numbers.end(),
        std::move(accumulate_promise)); // 求和
 
    // future::get() will wait until the future has a valid result and retrieves it.
    // Calling wait() before get() is not needed
    // accumulate_future.wait();  // wait for result
    std::cout << "result=" << accumulate_future.get() << '\n'; // 异步获取最后的结果
    work_thread.join();  // wait for thread completion
 
    //演示如何使用promise<int>在线程之间表示状态。
    std::promise<int> barrier; // 生成一个 std::promise<int> 对象.
    std::future<int> barrier_future = barrier.get_future(); // 和 future 关联.
    std::thread new_work1_thread(Do_work1, std::ref(barrier));
    std::thread new_work2_thread(Do_work2, std::ref(barrier_future));
 
    new_work1_thread.join();
    new_work2_thread.join();
    system("pause");
    return 0;
}
```



# std::packaged_task

```C++
#include <iostream>     // std::cout
#include <future>       // std::packaged_task, std::future
#include <chrono>       // std::chrono::seconds
#include <thread>       // std::thread, std::this_thread::sleep_for

// count down taking a second for each value:
int countdown (int from, int to) {
    for (int i=from; i!=to; --i) {
        std::cout << i << '\n';
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
    std::cout << "Finished!\n";
    return from - to;
}

int main ()
{
    std::packaged_task<int(int,int)> task(countdown); // 设置 packaged_task
    std::future<int> ret = task.get_future(); // 获得与 packaged_task 共享状态相关联的 future 对象.
    std::thread th(std::move(task), 10, 0);   //创建一个新线程完成计数任务.
    int value = ret.get();                    // 等待任务完成并获取结果.
    std::cout << "The countdown lasted for " << value << " seconds.\n";
    th.join();
    return 0;
}
```



# std::async

> [C++11之std::async使用介绍](https://blog.csdn.net/c_base_jin/article/details/79506347)

```cpp
//计算A函数
int calculateA()
{
    //延迟1ms
    this_thread::sleep_for(std::chrono::milliseconds(1));
    return 1;
}

//计算B函数
int calculateB()
{
    //延迟2ms
    this_thread::sleep_for(std::chrono::milliseconds(2));
    return 2;
}

//计算C函数
int calculateC()
{
    //延迟3ms
    this_thread::sleep_for(std::chrono::milliseconds(3));
    return 3;
}

//传统统计三者的和
void test_common_time()
{
    TIME_POINT t1 = STEADY_CLOCK::now();
    
    int c = calculateA() + (calculateB() + calculateC());
    
    TIME_POINT t2 = STEADY_CLOCK::now();
    
    cout << "test_common_time result is :" <<  c << "  ";
    print_diff_time(t1, t2);
}

// 使用异步线程
void test_feture_time()
{
	int sum = 0;
	
	TIME_POINT t11 = STEADY_CLOCK::now();
	
	//异步调用
	future<int> f1 = std::async(calculateA);
	future<int> f2 = std::async(calculateB);
	future<int> f3 = std::async(calculateC);
	//get()函数会阻塞，直到结果返回
	sum = f1.get() + f2.get() + f3.get();
	
	TIME_POINT t22 = STEADY_CLOCK::now();
	
	cout << "test_feture_time result is :" <<  sum << "  ";
	
	print_diff_time(t11, t22);       
}
```



# std::future

std::future是C++的一种模板类，它代表了一个异步操作的结果。通过使用std::future，我们可以将一个异步操作封装成一个对象，然后在需要的时候获取结果。通常，std::future是与另一个线程协同工作的结果。

## 使用

**(1) 创建std::future对象**

使用std::async函数来创建一个异步操作，并返回一个std::future对象：

```c++
std::future<int> fut = std::async(std::launch::async, [](){ /* 执行一些异步操作 */ });
```

这里使用std::async启动了一个异步操作，并返回一个std::future对象。这个异步操作可以是任意的函数或可调用对象，而返回值则是该操作的返回值。

**(2) 获取std::future的结果**

一旦异步操作完成，就可以通过调用std::future::get函数来获取结果。例如：

```c++
int result = fut.get(); // 阻塞等待结果并获取
```

通过调用fut.get()来获取异步操作的结果。如果结果还未就绪，调用get()将导致当前线程阻塞，直到结果就绪为止。

**(3) 异常处理**

当异步操作抛出异常时，我们可以使用std::future::get来获取异常信息。例如：

```C++
try {  
    fut.get(); // 获取结果并处理异常  
} catch (const std::exception& e) {  
    // 处理异常情况  
}
```

通过调用fut.get()来获取异步操作的结果。如果异步操作抛出了异常，那么这个异常将被传递给调用get()的线程，我们可以通过捕获异常来处理这种情况。

## 举例

定义一个简单的任务类Task，它接受一个整数参数作为标识符，并在执行时计算该标识符的两倍值并返回。然后，我们创建了一个包含4个任务的vector，并使用std::async函数将每个任务提交到线程池中。每个任务返回一个std::future<int>对象，代表了异步操作的结果。然后遍历所有的std::future对象，并通过调用get()函数获取结果。注意，调用get()函数会阻塞当前线程，直到结果就绪为止。最后，将每个任务的计算结果打印到终端。

```c++
#include <iostream>
#include <thread>
#include <future>
#include <vector>

// 定义一个简单的任务类
class Task {
public:
    Task(int id) : id(id) {}

    int operator()() {
        // 执行一些异步操作
        std::this_thread::sleep_for(std::chrono::seconds(2));
        return result = id * 2; // 计算结果
    }

    int getResult() const {
        return result;
    }

private:
    int id; // 任务的标识符
    int result; // 计算结果
};

int main() {
    // 创建一个包含4个任务的向量
    std::vector<Task> tasks = {Task(1), Task(2), Task(3), Task(4)};

    // 创建一个线程池，并提交任务到线程池
    std::vector<std::future<int>> futures;
    for (auto& task : tasks) {
        futures.push_back(std::async(std::launch::async, task));
    }

    // 遍历未来的结果，并打印出来
    for (auto& future : futures) {
        std::cout << "Result: " << future.get() << std::endl; // 阻塞等待结果并获取
    }

    return 0;
}
```

