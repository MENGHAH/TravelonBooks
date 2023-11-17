# 配置步骤

(1) 取消core文件大小限制
```shell
ulimit -c unlimited
```
(2) 配置core文件产生路径
```shell
mkdir -p /corefile
sudo bash -c 'echo /corefile/core-%e-%p-%t > /proc/sys/kernel/core_pattern'
cat /proc/sys/kernel/core_pattern
```
(3) 测试core文件生成
```shell
kill -s SIGSEGV $$
```
(4) 查看core文件存储位置
core dump 存放在哪个目录是由系统参数kernel.core_pattern决定的。可以通过如下指令查询：

```shell
cat /proc/sys/kernel/core_pattern
```

# 参考文章

- [linux coredump测试](https://www.cnblogs.com/jefree/p/4439035.html)

