# 注意

直接使用 apt-get install ffmpeg安装的ffmpeg是没有头文件的， 在C/C++程序中无法使用。需要手动安装编译才能gcc或者g++编译时使用

# ffmpeg安装

```shell
// 自定义安装路径
./configure --prefix=/usr/local/ffmpeg --enable-shared --enable-gpl --disable-x86asm
make -j8 && make install
```

# 使用举例

```cpp
#include <iostream>
extern "C" {
    #include <libavformat/avformat.h>
}

int main() {
    av_register_all();

    // 这里可以进行 ffmpeg 的相关操作
    return 0;
}

/**
 * 编译指令：主意include的路径和lib的链接路径
 *  g++ -o example example.cpp -I/usr/local/ffmpeg/include -L/usr/local/ffmpeg/lib -lavformat -lavcodec -lavutil
 */
```

