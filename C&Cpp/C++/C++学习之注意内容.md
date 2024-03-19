# C++编程中易错点

## 类或者结构体作为map的key

只有重载<的类或者结构才能作为map的key值。string可以作为key值是因为string重载了<。如果不重载<会提示如下错误：
error C2676: 二进制“<”: “const C”不定义该[运算符](https://so.csdn.net/so/search?q=运算符&spm=1001.2101.3001.7020)或到预定义运算符可接收的类型的转换

```C++
#include <string>
#include <map>

using namespace std;

// 重载<的类或结构才能作为map的key值
class C
{
public:
    int i;
    string str;
    bool operator < (const C &c) const
    {
        return i < c.i;
    }
};

void main()
{
    map<C,int> mapC;
    C c0;
    c0.i = 0;
    c0.str = "str1";
    mapC.insert(pair<C,int>(c0,0));
    C c1;
    c1.i = 1;
    c1.str = "str2";
    mapC.insert(pair<C,int>(c1,1));
    
    if(c0 < c1)
        int iVal = mapC[c1];

    // string可以作为map的key,因为重载了<
    string str1 = "str1",str2 = "str2";
    if(str1 < str2) // "str2"是大于"str1"的
    {
        int n = 0;
    }
}
/* 如果不重载<会提示如下错误：
 * error C2676: 二进制“<”: “const C”不定义该运算符或到预定义运算符可接收的类型的转换
 * 重载<但是没有实现会提示如下错误：
 * Expression: invalid operator<
 * 比如bool operator < (const C &c) const{return true;}
 */
```

