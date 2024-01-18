# 1. 飞行堡垒8安装win10+ubuntu16.04双系统

> 电脑配置：i7-10750H + GTX1660Ti

## 1.1 Windows下如何删除磁盘分区

> 1. [删除磁盘分区](https://www.sohu.com/a/343036265_119438)

**方法一：使用DiskPart删除分区**

DiskPart是一个Windows磁盘管理工具。与磁盘管理不同，它使用命令行（CMD）来执行任务。 DiskPart删除分区命令行是一种用语法删除分区的好方法。

1. 打开命令提示符，键入：diskpart
2. 在diskpart提示符下，键入：list disk

然后，计算机上的所有磁盘都将列出，您应该找出要从中删除分区的磁盘的磁盘编号。

1. 键入：select disk n

n是磁盘编号。 选择要删除的分区的磁盘号。

1. 键入：list partition
2. 然后，键入：select partition n

n是分区编号。在这里，您需要选择要删除的分区。

1. 键入：delete partition

​      有时候第6步会显示出错，那么就使用 delete partition override 命令进行删除

然后，使用DiskPart删除分区将显示如下屏幕截图所示：

![image](https://cdn.nlark.com/yuque/0/2020/png/8366396/1607678961256-aeb71732-6f40-4c55-ad52-26f5d4868ce3.png)



## 1.2 NVIDIA驱动安装

1） 官网下载显卡驱动

> https://www.nvidia.cn/drivers/results/150808/



## 1.3 ubuntu内核升级

> [1. Ubuntu 16.04 手动安装无线网卡驱动（连接WiFi）](https://blog.csdn.net/qq_35584408/article/details/106534038)
>
> [2. 联想 Yoga C740：关于Ubuntu16.04下无法识别Intel WIFI6 AX201无线网卡的解决方案](https://blog.csdn.net/dieju8330/article/details/101422743)

## 1.4 其他问题

### 1.4.1 无法连接wifi

1. [连接wifi故障](https://blog.csdn.net/qq_35584408/article/details/106534038)

> 升级Linux内核版本到5.4，然后手动安装无线驱动后解决无法连接wifi的问题



1）查看当前Linux内核版本

```
uname -sr
```



2）下载5.4版本的Linux内核

下载如下图所示的4个文件到home下面，路径需要是全英文的

![image](https://cdn.nlark.com/yuque/0/2021/png/8366396/1611208284084-8773f1a4-5195-4a62-a24b-6818d4e95399.png)



3）安装

```
sudo dpkg -i *.deb
```

如果报错提示：依赖libssl1.1.0的问题，需要额外下载安装libssl1.1_1.1.0g-2ubuntu4.1_amd64.deb文件，然后再升级就可以了。解决如下：

```
wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb
sudo dpkg -i libssl1.1_1.1.0g-2ubuntu4_amd64.deb
```

安装完成后需要重启，再次查看内核版本是否满足需求



4）下载网卡驱动

网址：http://ftp.sjtu.edu.cn/ubuntu/pool/main/l/linux-firmware/

选择linux-firmware的deb文件下载：


![image](https://cdn.nlark.com/yuque/0/2021/png/8366396/1611208421413-5e8b4c1c-4ade-4548-aec5-2ee4a049427e.png)



5）安装并重启

```
sudo dpkg -i linux-firmware_1.187_all.deb 
```

### 1.4.2 无法使用扩展屏



### 1.4.3 修改分辨率



> 可以直接修改grup文件来强制更改分辨率，但是这不是一个很好的办法。



首先打开终端，输入



```
sudo gedit /etc/default/grub
```



找到语句 #GRUB_GFXMODE=800x600，修改为你需要的分辨率，如：#GRUB_GFXMODE=1920x1080，并删除前面的‘#’使该语句生效。保存并退出



终端输入



```
sudo update-grub
```



最后重启计算机，即可改变分辨率，并能够在设置显示中更换多种分辨率



### 1.4.4 修改显示字体的大小



### 1.4.5 win+ubuntu时间同步问题



**使用ntpdate校准时间**



- 安装ntpdate



```
sudo apt-get install ntpdate
```



- 校准时间



```
sudo ntpdate time.windows.com
```



- 如果你是安装的Windows+Ubuntu双系统，需要将时间更新到硬件上，以免Windows的时间不对



```
sudo hwclock --localtime --systohc
```



# 2. Ubuntu.deb包安装方法



> [Ubuntu .deb包安装方法](https://blog.csdn.net/zhaoyang22/article/details/4235596)



# 3. Anaconda3安装以及环境配置

> 1. [ubuntu下安装anconda](https://blog.csdn.net/qq_15192373/article/details/81091098)



1.Anaconda3的安装



- 清华镜像下载Anaconda3
- `bash Anaconda3-4.4.0-Linux-x86_64.sh` 
- 修改环境变量

```
1. sudo gedit ~/.bashrc
2. export PATH="/home/xupp/anaconda3/bin:$PATH"
3. source ~/.bashrc
```



2.安装tensorflow



> pip install tensorflow
>
> pip install tensorflow==1.14.0



3.安装pytorch



> pip install torch



# 4. Java开发环境安装



### JDK安装



1、更新软件包列表：



```
sudo apt-get update
```



2、安装openjdk-8-jdk：



```
sudo apt-get install openjdk-8-jdk
```



3、查看java版本，看看是否安装成功：



```
java -version
```



### eclipce安装



直接官网下载安装包解压缩就可以



# 5. LaTeX安装



## TexLive安装



```
sudo apt-get install textlive-full
sudo apt-get install texstudio
```



TeXstudio中在Options->Configure TeXstudio->Build->Default Compiler中更改默认编译器为XeLaTeX。



Options->Configure TeXstudio->General->Language更改为zh-CN。



```
测试代码：
\documentclass[utf8]{ctexart}

\title{深度学习}
\author{匿名}

\begin{document}
    \maketitle
\end{document}
```



**另外一个安装方法：**



> https://blog.csdn.net/wuguangbin1230/article/details/78017013



**直接sudo安装会比较慢，可以利用清华源镜像安装：**



> https://blog.csdn.net/williamyi96/article/details/90732304（亲测好用）



## TeXsdudio安装



```
sudo apt-get install texstudio
```



如果上述命令不成功就先执行如下命令：



```
sudo add-apt-repository ppa:sunderme/texstudio
```



再执行上述 `sodu apt-get install texstudio`



# 6. Deepin wine安装



前往github仓库，里面有安装方法



```
https://github.com/wszqkzqk/deepin-wine-ubuntu
```



**注意： 如果前期ubuntu没有安装git，需要先安装git**



```
sudo apt install git
```



# 7. Vim的使用和退出



编辑文件可按下 i 、 a 、 o 键

退出

按 Esc ,输入 :q ;如果你已经修改了文件想放弃:输入 :q! ;如果想保存 输入 :wq



# 8. 安装搜狗拼音



```
wget -c "https://pinyin.sogou.com/linux/download.php?f=linux&bit=64" -O "sogoupinyin_amd64.deb"
sudo dpkg -i sogoupinyin_amd64.deb
sudo apt -f -y install
sudo dpkg -i sogoupinyin_amd64.deb
```



如果上述方式没有成功，就按照此[博客](https://blog.csdn.net/leijieZhang/article/details/53707181?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.not_use_machine_learn_pai&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.not_use_machine_learn_pai)来解决。



# 9. 安装网易云音乐



```
wget "http://d1.music.126.net/dmusic/netease-cloud-music_1.1.0_amd64_ubuntu.deb" -O "netease-cloud-music_amd64_ubuntu.deb"
sudo dpkg -i netease-cloud-music_amd64_ubuntu.deb
sudo apt-get -f install
sudo dpkg -i netease-cloud-music_amd64_ubuntu.deb
```



# 10. 安装谷歌浏览器



```
wget "https://dl.google.com/linux/direct/google-chrome stable_current_amd64.deb" -O "google-chrome-stable_current_amd64.deb"
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt -f -y install
```



# 11. 安装 git



```
sudo apt -y install git
```



**GitHub 的使用参考这个网站:**



> https://blog.csdn.net/ajianyingxiaoqinghan/article/details/70544159



# 12. 安装 sublime text3



**安装 GPG:**



```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
```



**确保 apt 被设置为 https 源**



```
sudo apt-get install apt-transport-https
```



**选择稳定版本**



```
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
```



**安装 sublime-text**



```
sudo apt-get update
sudo apt-get install sublime-text
```



# 13. 安装 typora



**一款好用的 markdown 编辑器**



```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
sudo apt update
sudo apt install typora
```



# 14. 安装 arc-flatabulous 主题



```
sudo add-apt-repository ppa:noobslab/themes
sudo apt-get update
sudo apt-get install arc-flatabulous-theme
```



# 15. 安装 ultra-flat 图标



```
sudo add-apt-repository ppa:noobslab/icons
sudo apt-get update
sudo apt-get install ultra-flat-icons
```



# 16. 安装 fish shell



```
sudo apt-add-repository ppa:fish-shell/release-2
sudo apt-get update
sudo apt-get install fish
```



# 17. 安装截图软件 shutter



**(ubuntu18.04可以直接在商店下载)**



```
sudo add-apt-repository ppa:shutter/ppa
sudo apt-get update
sudo apt-get install shutter
```



# 18. 安装防蓝光软件 redshift



```
sudo apt-get install redshift
```



# 19. 安装 Foxit Readerwget



```
wget http://cdn07.foxitsoftware.cn/pub/foxit/reader/desktop/linux/2.x/2.1/en_us/FoxitReader2.1.0805_Server_x64_enu_Setup.run.tar.gz
tar xvzf FoxitReader2.1.0805_Server_x64_enu_Setup.run.tar.gz
chmod +x FoxitReader.enu.setup.2.1.0805\(r225432\).x64.run
sudo ./FoxitReader.enu.setup.2.1.0805\(r225432\).x64.run
```



**卸载**

使用安装目录里面的maintenancetool 。通常的安装目录为home/(user)/opt/foxitsoftware/foxitreader

也可以通过locate foxit查找目录

这可能需要root权限，使用sudo ./maintenancetool



# 20. 安装 WPS



可以直接官网下载解压缩



```
wget "<http://kdl.cc.ksosoft.com/wps-community/download/a21/wps[[http://kdl.cc.ksosoft.com/wps-community/download/a21/wps-office_10.1.0.5672~a21_amd64.deb|-]][[office_10.1.0.5672~a21_amd64.deb]]>" -O "wps-office_amd64.deb"
sudo dpkg -i wps-office_amd64.deb
```



然后下载缺失的字体文件,

然后复制到 Linux 系统中的 /usr/share/fonts 文件夹中。

国外下载地址:



> https://www.dropbox.com/s/lfy4hvq95ilwyw5/wps_symbol_fonts.zip



国内下载地址:



> https://pan.baidu.com/s/1eS6xIzo



下载完成后,解压并进入目录中,继续执行:



```
sudo cp * /usr/share/fonts
```



执行以下命令 , 生成字体的索引信息:



```
sudo mkfontscale
sudo mkfontdir
```



运行 fc-cache 命令更新字体缓存。



```
sudo fc-cache
```



重启 wps 即可,字体缺失的提示不再出现。



# 21. 转换文件编码



傻瓜型命令行工具 enca ,它不但能智能的识别文件的编码,而且还支持成批转换。



1. 安装
   sudo apt-get install enca
2. 查看当前文件编码
   enca -L zh_CN ip.txt
3. 转换
   命令格式如下
   enca -L 当前语言 -x 目标编码 文件名



- 例如要把当前目录下的所有文件都转成 utf-8
  enca -L zh_CN -x utf-8 *
- 检查文件的编码
  enca -L zh_CN file
- 将文件编码转换为 "UTF-8" 编码
  enca -L zh_CN -x UTF-8 file
- 如果不想覆盖原文件可以这样
  enca -L zh_CN -x UTF-8 < file1 > file2



# 22. 在 ubuntu 下解压 rar 文件



在终端中输入命令安装压缩程序 rar 和解压缩的 unrar.



```
sudo apt-get install rar unrar
sudo apt-get install rar rar
```



Linux中解压 rar 类型文件的命令为:



```
unrar e file.rar # 把原 rar 压缩包中的全部文件解压到当前目录下,没有目录
或者
 rar x file.rar # 把原 rar 压缩包中的全部
```

# 23. 安装MySQL

> 1. [ubuntu安装mysql和简单操作](https://www.cnblogs.com/lfri/p/10437694.html)

# 24. pip相关

**安装pip**

```
sudo apt install python3-pip
```

**卸载pip**

```
sudo apt remove  python3-pip
```

**pip换源**

> 1. [ubuntu 下pip 换源](https://blog.csdn.net/wangdong1186/article/details/109267043)

**ubuntu下更换默认python版本**

> 1. [Ubuntu修改默认Python版本](https://blog.csdn.net/White_Idiot/article/details/78240298)[ubuntu下安装anconda](https://blog.csdn.net/qq_15192373/article/details/81091098)

# 25. ubuntu安装tensorflow

```
pip install tensorflow==1.14.0 # 此命令安装的是CPU版本的tensorflow
```

# 26. ubuntu安装pytroch和torchvision

> 1. [ubuntu下pytorch和torchvision的安装](https://blog.csdn.net/qq_40263477/article/details/106577790)

- 先更新conda 

```
conda updade conda
```

- 安装cpu版本的pytorch和torchvision

```
conda install pytorch-cpu==1.1.0 torchvision-cpu==0.3.0 cpuonly -c pytorch
```