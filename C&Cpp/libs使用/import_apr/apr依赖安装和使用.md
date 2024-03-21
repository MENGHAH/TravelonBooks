# APR安装

```
./configure
make && make install
```

# 使用举例

```cpp
#include <iostream>
#include <string.h>
#include <stdio.h>
#include <algorithm>
#include <chrono>
#include <thread>

#include <apr.h>
#include <apr_pools.h>
#include <apr_file_io.h>
#include <stdlib.h>
#define POOL_NUMBER 32
#define ALLOCAT_NUMBER 32

using std::chrono::seconds;

typedef struct recorder_channel_t{
    char buf[1024*1024*100];
    char *channel_id;
};

void aprDemo()
{
	apr_pool_t *pool[POOL_NUMBER];
    apr_status_t status;
    std::string console_out;
    
    for(int i=0; i<POOL_NUMBER; i++)
    {
        console_out = "pool[" + std::to_string(i) + "]";
        std::cout << console_out << std::endl;
        pool[i] = NULL;
        status = apr_pool_create(&pool[i], NULL);
        std::cout << status << std::endl;
        for(int j=0; j<ALLOCAT_NUMBER; j++){
            std::this_thread::sleep_for(seconds(1));
            recorder_channel_t* temp = static_cast<recorder_channel_t*>(apr_palloc(pool[i], sizeof(recorder_channel_t)));
            memset(temp->buf, '0', 1024*1024*100);
        }
        apr_pool_destroy(pool[i]);
    }
}

int main() {
    apr_initialize();
    aprDemo();
    return 0;
}

/**
 * 编译指令：
 * g++ -o test apr_test.cpp -I/usr/local/apr/include/apr-1/ -L/usr/local/apr/lib -lapr-1 -lpthread -std=c++11
 */
```

