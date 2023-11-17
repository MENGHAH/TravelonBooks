在选择服务器时有可能需要知道服务器的规格，具体一个Linux服务器规格的查看可以参照如下的命令行进行查阅
- 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
- 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

(1) 查看物理CPU个数
```shell
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
```
(2) 查看每个物理CPU中core的个数(即核数)
```shell
cat /proc/cpuinfo| grep "cpu cores"| uniq
```
(3) 查看逻辑CPU的个数
```shell
cat /proc/cpuinfo| grep "processor"| wc -l
```
> 当计算机没有开启超线程时，逻辑CPU的个数就是计算机的核数。 而当超线程开启后，逻辑CPU的个数是核数的两倍。 实际上逻辑CPU的数量就是平时称呼的几核几线程中的线程数量，在linux的cpuinfo中逻辑CPU数就是processor的数量

(4) 内存信息
```shell
free -g
```

(5) 查看CPU信息（型号）

```shell
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```

(6) 查看内存信息

```shell
cat /proc/meminfo
```

(7) 如何查看Linux 内核

```shell
uname -a
cat /proc/version
```

(8) 查看机器型号（机器硬件型号）

```
dmidecode | grep "Product Name"
dmidecode
```

(9) 如何查看linux 系统版本

```shell
cat /etc/redhat-release
lsb_release -a
cat  /etc/issue
```

(10) 如何查看linux系统和CPU型号，类型和大小

```shell
cat /proc/cpuinfo
```





 