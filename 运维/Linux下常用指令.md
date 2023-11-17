# 1. 文件压缩与解压
**[tar的使用]**
- https://blog.csdn.net/user836728528852/article/details/123802581
- http://c.biancheng.net/view/3976.html

```shell
#文件压缩
tar -cvf file.tar.gz file
#解压缩
tar -xvf file.tar.gz
```
# 2. 文件传输
**(1) 文件的上传和下载**
```shell
文件下载：sz
文件上传：rz
```
**也可以通过winscp等工具进行文件的传输**
**(2) 服务器之间的文件传输**
```shell
传输单个文件 scp target_file admin@server_ip:/targe/path 
传输单文件夹 scp -r target_file admin@server_ip:/targe/path
```
# 3. vim
**3.1 批量注释**
(1) 在 10 - 20 行添加 // 注释
```
:10,20s#^#//#g
```
(2) 在 10 - 20 行删除 // 注释
```
:10,20s#^//##g
```
(3) 在 10 - 20 行添加 # 注释
```
:10,20s/^/#/g
```
(4)在 10 - 20 行删除 # 注释
```
:10,20s/#//g
```
**3.2 批量字符替换**
(1) 将def对abc进行全局替换
```
:%s/abc/def/g
```
# 4. Ubuntu
搜索某个包是否可用
```shell
sudo apt-cache search libgsl 
```
输出如下
```shell
libgsl-dbg - GNU Scientific Library (GSL) -- debug symbols package                                                                                                       
libgsl-dev - GNU Scientific Library (GSL) -- development package                                                                                                         
libgsl23 - GNU Scientific Library (GSL) -- library package                                                                                                               
libgslcblas0 - GNU Scientific Library (GSL) -- blas library package                                                                                                      
libocamlgsl-ocaml - GNU scientific library for OCaml                                                                                                                     
libocamlgsl-ocaml-dev - GNU scientific library for OCaml 
```
从上述包中选择一个进行安装
```shell
sudo apt-get install libgsl-dev
```
# 5.Git
https://blog.csdn.net/zhezhebie/article/details/78496693
(1) 在当前分支下切出来新分支
```shell
git checkout -b dev-release-android11 # 这里的dev-release-android11就是新分支的名字
```
(2) 关联到远程仓库
```shell
git push --set-upstream origin dev-release-android11
```
(3) 查看是否关联成功
```shell
git branch -vv
```
(4) 将某个分支的所有修改合并到master
首先切换到master分支
```shell
git merge dev-name
```

(5) git版本回退
```shell
git log
git reset --hard 版本号
```
(6) 查看当前没有add 的内容修改
```shell
git diff
```
(7) 查看某个提交的具体修改
```shell
git show 版本号
```
(8) 向当前分支合并指定提交
```shell
# feature 分支上的某次commit 62ecb3要合并到master
git checkout master
git cherry-pick 62ecb3
```
(9) 查看任意两个版本之间的改动
```shell
git diff 版本号码1 版本号码2
```
(10) 比较两个版本号码的src 文件夹的差异
```shell
git diff 版本号码1 版本号码2  src
```
(11) 压缩多个commit为一个
https://kinboyw.github.io/2019/04/09/Git-%E5%8E%8B%E7%BC%A9%E5%A4%9A%E4%B8%AAcommit%E4%B8%BA%E5%8D%95%E4%B8%AAcommit/
(12) 查看当前分支的本地修改
```shell
git diff
```
(13) 远程分支的强制回滚
```shell
git checkout current_branch_name # 切换到当前分支
git reset --hard commit_id # 回滚到某次提交
git push -f origin current_branch_name # 强制回滚
```
(14) 查看远程所有分支
```shell
git branch --all
git checout 
```
(15) 切换到某个远程分支或者本地分支
```shell
git checkout 分支名称
```
# 6.Linux
```
https://www.myfreax.com/how-to-uninstall-software-packages-on-ubuntu/
```
**常用指令**
- cd
- ls
- pwd
- cd
- cp src_file dst_file	拷贝文件src_file并命名为dst_file
- mv src_filr dirpath/ 将src_file移动到dirpath文件夹下
- mkdir target_path 创建名为target_parth的文件夹
- touch src_file 创建一个空的文件
	- touch test.docx 就是创建一个test文档
	- touch test.sh 就是创建一个名为test的shell脚本
- cat target_file 输出并查看traget_file的内容 


**基本指令**


(1) 全局文件搜索
```
sudo find / -name dialplan 使用文件查找命令直接查找dialplan目录
```
(2) 获取本地的ip
```
cti_ip=`ifconfig |grep inet|grep -v 127.0.0.1|grep -v inet6|awk '{print $2}'|tr -d "addr:"`
grep：过滤字段
```
(3) 获取8021端口连接建立的数量
```
extablished_num=`netstat -anp | grep jdcc-run | grep -i "8021" | wc -l`
```
(4) 获取8021端口连接的具体ip
```
NR==1 是只取第一行
netstat -na|grep 8021|awk '{print $5}'|awk -F: 'NR==1{print $1}'
```
(5) telnet指令
```
当某个服务器无法连接时可以尝试使用telnet指令对服务器的指定ip进行通信
telnet 127.0.0.1 19001
```
(6) 查找含有某个字段的所有文件
```
https://www.runoob.com/linux/linux-comm-find.html
find . | xargs grep string #查找当前目录下文件内容包含字符串string的文件
find / -name file_name #查找文件名为file_name的文件
```
(7) nc指令向固定IP和port发送指令
```
nc IP Port < commands.txt
```
(8) 创建软连接
```
在当前路径下创建test，并将test软连接到/var/www路径下的test
ln -s /var/www/test test
```
(9) 杀掉包含所有mysql字段的进程
```
ps -ef|grep mysql|grep -v grep|cut -c 9-15|xargs kill -9  
```
(10) 对比文件夹中各个文件的不同
```
diff -Naur linux-2.6.8.1 linux-2.6.8.1-xyzzy
```
(11) 查询指令
```
查找目录下的所有文件中是否含有某个字符串
find .|xargs grep -ri "xxxx"
查找目录下的所有文件中是否含有某个字符串,并且只打印出文件名
find .|xargs grep -ri "xxxx" -l 
```
(12) grep使用
```
https://www.runoob.com/linux/linux-comm-grep.html
```
(13) 批量删除
需要删除/data/local/tmp路径下以“.xml”结尾的文件
```
find . -name "*.xml" -exec rm -f {} \;
```
说明
rm -f {} : 删除时，不提示，{}表示查找到的文件
## 6.1 grep的使用
**(1) 或操作**

- grep -E '123|abc' filename  // 找出文件（filename）中包含123或者包含abc的行
- egrep '123|abc' filename    // 用egrep同样可以实现
- awk '/123|abc/' filename   // awk 的实现方式

**(2) 与操作**
- grep pattern1 files | grep pattern2 //显示既匹配 pattern1 又匹配 pattern2 的行。

**(3) 其他操作**
- grep -i pattern files   //不区分大小写地搜索。默认情况区分大小写，
- grep -l pattern files   //只列出匹配的文件名，
- grep -L pattern files   //列出不匹配的文件名，
- grep -w pattern files  //只匹配整个单词，而不是字符串的一部分（如匹配‘magic’，而不是‘magical’），
- grep -C number pattern files //匹配的上下文分别显示[number]

**(4) 递归查找**
```shell
grep -r -ni "target_string" target_path // -i 参数是不区分大小写
```
## 6.2 sed的使用
**(1) 批量递归查找和替换目标字符串**

- 方式一：
```shell
sed -i "s/原字符串/替换后字符串/g" `grep '搜索关键字' -rl /data/目标目录/ --include "*.html"`

比如：
sed -i "s/MYSQLTP:root:123456/MYSQLTP:freeswitch:freeswitch/g" `grep "MYSQLTP:root:123456" -rl ./`
```

- 方式二：
```shell
sed -i "s/yyyy/xxxx/g" |xargs grep yyyy -rnl ./

比如：
sed -i "s/MYSQLTP:root:123456/MYSQLTP:freeswitch:freeswitch/g" |xargs grep "MYSQLTP:root:123456" -rnl ./
```
# 7. freeswitch
(1) 查看freeswitch的注册信息
```
sofia status profile internal
```
(2) 查看注册分机号
```
sofia status profile internal reg
```
(3) freeswitch日志查看
https://blog.csdn.net/jiayangang/article/details/79367688
```
日志级别
直接F8也可以开启loglevel7
console loglevel （0~7）
打开sip详细日志
sofia profile internal siptrace on
关闭sip详细日志
sofia profile internal siptrace off
```
(4) 通过指定网关外呼指定号码并播放回音
```
originate sofia/gateway/gw1/xxxxxx &echo()
originate sofia/gateway/jdcc-test/123321654 &echo()
```
(5) 其他常用指令
- 通过命令行执行fs控制台命令：fs_cli -x "reloadxml"
- 查看sofia模块状态：sofia status
- 查看freeswitch状态：status
- 查看通话命令：show calls
- 打开log命令：console loglevel 7
- 关闭log命令：console loglevel 0
- 开启全局信令追踪：sofia global siptrace on
- 关闭全局信令追踪：sofia global siptrace off
- 发起一个通话：originate 呼叫字符串 &fsApp
- 挂断所有通话：hupall
- 查看注册的话机 sofia status profile internal reg
- 查询分机号是否注册 sofia status profile internal reg num
- 清空所有注册话机 sofia profile internal flush_inbound_reg
- 查看已加载的module： unload mod（tab）
- 设置最大session数：fsctl max_sessions [int]
- 网关reload：sofia profile external restart
- 加载模块 reload mod_XXX
- 设置日志输出等级：fsctl loglevel 7
- 获取系统参数值：global_getvar name
- 设置系统参数：global_setvar <varname>=<value>
- 清空所有注册信息：sofia profile internal flush_inbound_reg
- 订阅freeswitch所有event事件：/event all
- 执行js脚本：jsrun test.js
- 最近1秒的sps：fsctl last_sps
- 设置sps 上限：fsctl sps [int]
- 发送info消息：uuid_send_info uuid msg

# 8. mysql
(1) 启动mysql

```shell
service mysql start # 首先启动mysql的服务
mysql -u root -p

# 重置密码
update mysql.user set authentication_string=password('123456') where user='root'
```

(2) 完全卸载mysql并重新安装
https://blog.csdn.net/chudongfang2015/article/details/52154903

# Redis
(1) redis修改端口
Redis在redis.conf中修改端口号后使用如下指令启动server
./redis-server ../redis.conf

gcc升级
(1) 源码编译安装
https://www.cnblogs.com/music-liang/p/12900457.html
(2) 通过yum安装
https://www.cnblogs.com/jixiaohua/p/11732225.html
Vim
(1) 多个匹配项在同一行
/.*red\&.*blue

(2) 进行整行的替换
%s/foo/bar/g # 用bar对foo进行全文替换

# GDB
gdb常用的调试命令
https://blog.csdn.net/Jin_Kwok/article/details/80104022
b：设置断点
n:单步运行（不跳入函数）
s:单步运行（跳入函数）
l 1,100:查看源代码

# Python
python在后台运行脚本
https://blog.csdn.net/weixin_42782150/article/details/104475814

province_info.log是运行的日志
python province_main.py >province_info.log &

# Tcpdump
(1) 地指定网卡抓包并保存到本地

```shell
sudo tcpdump -i 网卡 port 1234 -w mid.pcap

# 抓指定端口的UDP包
sudo tcpdump -i any port 1234 udp -w mid.pcap
```

# Sipp使用
如何在服务器安装sipp
https://www.techrepublic.com/article/how-to-install-the-sipp-testing-tool-on-ubuntu-server-18-04/
- 下载对应版本的压缩包直接解压缩

- 安装相应的依赖库

- 执行下列编译指令

  ```shell
  cd /path/to/sipp
  cmake . -DUSE_SSL=1 -DUSE_SCTP=1 -DUSE_PCAP=1 -DUSE_GSL=1
  make
  cp sipp /usr/local/bin
  ```

sipp各个参数的含义

```shell
sipp -sn uac 172.31.89.4:5060 -r 1 -rp 3000 -inf data.csv -p 7098 -i 172.31.89.242 -s 8001 -sf uac_onecall.xml –m 1000 –l 900
```

各个参数说明：
- 172.31.89.4:5060：远端地址和端口（在脚本中用[remote_ip]，[remote_port]引入）
- -r 1 -rp 3000：每三秒钟发一个呼 (-r 表示单位时间内发出的呼叫个数， -rp表示定义的单位时间)
- -inf data.csv：引入数据配置文件
- -s 8001：被叫号码（在脚本中用[service]引入）
- -sf uac_onecall.xml：引入脚本文件，根据需要模拟的呼叫流程编写
- -sn uac :执行默认的uac流程，如需执行自己编写的流程文件，命令中应不含此参数
- -m 1000:发送1000次呼叫后停止并退出。
- -trace_screen -screen_overwrite true ：在保存屏幕文件时，覆盖屏幕文件（默认为true）。
- -screen_file uac.log：设置屏幕文件的名称。
- -r 50 是每秒发送50条SIP消息
- -i 172.31.89.242：本地地址（在脚本中用[local_ip]引入）
- -p 7098：本地端口（在脚本中用[local_port]引入）
- -l 900 :设置最大的并发呼叫量
- caps呼叫时长，当因种种原因导致现存呼叫总数达到此值时，SIPp将停止产生新的呼叫，等待现存呼叫总数低于此值时才继续产生呼叫。