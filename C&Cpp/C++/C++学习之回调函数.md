# 基本库函数介绍

## 1. std::function





## 2. std::bind



# 普通回调函数实现





# 基于std::function回调函数实现

```c++
#include <iostream>  
#include <functional>  

using namespace std;
using namespace std::placeholders;

typedef std::function<void(int, int)> static_func_cpp11;
typedef std::function<void(int, int)> func_cpp11_callback;

class CallbackWrapper {
public:
    static void static_callback(int a, int b)
    {
        cout << "Static callback() was called!" << endl;
    }

    void callback(int a, int b)
    {
        cout << "callback() was called!" << endl;
    }

    void virtual virtual_callback(int a, int b)
    {
        cout << "virtual callback() was called!" << endl;
    }

};

class CallbackWrapper2 : public CallbackWrapper {

    void virtual_callback(int a, int b)
    {
        cout << "CallbackWrapper2 virtual callback() was called!" << endl;
    }

};

void call_func(int a, int b, std::function<void(int, int)> func)
{
    cout << "call func:";
    func(1, 2);
}

int main()
{
    CallbackWrapper callbackWrapper;
    CallbackWrapper2 callbackWrapper2;

    //C++11 static
    static_func_cpp11 static_callback_ = std::bind(&CallbackWrapper::static_callback, _1, _2);
    static_callback_(1, 2);
    call_func(1, 2, static_callback_);
    cout << "*********************************" << endl;

    //C++11 non static
    func_cpp11_callback callback_ = std::bind(&CallbackWrapper::callback, &callbackWrapper, std::placeholders::_1, std::placeholders::_2);
    callback_(1, 2); // 回调函数直接调用
    call_func(1, 2, callback_); // 回调函数作为参数
    cout << "*********************************" << endl;
    
    return 0;
}
```

