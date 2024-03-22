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
#include <vector>

#include <apr.h>
#include <apr_pools.h>
#include <apr_file_io.h>
#include <stdlib.h>

#define THREAD_POOL_SIZE 6
#define POOL_NUMBER 32
#define ALLOCAT_NUMBER 10
#define BUFFER_SIZE 1024*1024*32

using std::chrono::seconds;

typedef struct recorder_channel_t{
    char buf[BUFFER_SIZE];
    char *channel_id;
};

void aprDemo(int sleep_time)
{
	apr_pool_t *pool[POOL_NUMBER];
    apr_status_t status;
    std::string console_out;
    
    std::cout << console_out << "thread_id: " << std::this_thread::get_id() << std::endl;
    
    for(int i=0; i<POOL_NUMBER; i++)
    {
        console_out = "pool[" + std::to_string(i) + "]";
        {
            pool[i] = NULL;
            status = apr_pool_create(&pool[i], NULL);
            printf("address: %p", pool[i]);
            std::cout << status << std::endl;
            for(int j=0; j<ALLOCAT_NUMBER; j++){
                std::this_thread::sleep_for(seconds(sleep_time));
                recorder_channel_t* temp = static_cast<recorder_channel_t*>(apr_palloc(pool[i], sizeof(recorder_channel_t)));
                memset(temp->buf, '0', BUFFER_SIZE);
            }
            apr_pool_destroy(pool[i]);
        }
    }
}

void threadPoolCreate()
{
    std::vector<std::thread> th_pool;
    for(int i=0; i<THREAD_POOL_SIZE; i++)
    {
        std::thread th(aprDemo, i);
        th_pool.push_back(std::move(th));
    }

    for (auto& thread : th_pool){
        thread.join();
    }
}

int main() {
    apr_initialize();
    threadPoolCreate();
    return 0;
}

/**
 * 编译指令：
 * g++ -o test apr_test.cpp -I/usr/local/apr/include/apr-1/ -L/usr/local/apr/lib -lapr-1 -lpthread -std=c++11
 */
```

