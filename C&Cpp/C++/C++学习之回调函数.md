# 基本库函数介绍

## 1. std::function



## 2. std::bind



# 普通回调函数实现





# 基于C++11新特性的回调函数实现

```c++
#include <iostream>
#include <functional>
#include <vector>
#include <assert.h>

using namespace std;

double callableFunc (double x, double y) 
{
    return x/y;
}


class Base{
public:
    int func_sum(int x, int y, int z)
    {
        std::cout << x << std::endl;
        std::cout << y << std::endl;
        std::cout << z << std::endl;
        return x + y + z;
    }

    int m_data = 30;
};


int main()
{
    // 绑定一个普通函数
    auto NewCallable = std::bind (callableFunc, std::placeholders::_1, 2); // 绑定普通函数
    std::cout << NewCallable (10) << '\n';  

    Base base;
    // 绑定类中的一个成员函数
    auto newiFunc = std::bind(&Base::func_sum, &base, 100, std::placeholders::_1, std::placeholders::_2);
    int res = newiFunc(20, 100);
    std::cout << res << std::endl;

    return 0;
}
```

