# std::promise



# std::packaged_task



# std::async



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

