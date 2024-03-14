## 一、docker几个核心对象[#](https://www.cnblogs.com/jiujuan/p/13758351.html#2148116572)

1. 镜像 image
2. 容器 container
3. 卷 volumes
4. 网络 network

docker的镜像是由 镜像名+版本 组成的。如果没有指定镜像名没有指定版本，默认是latest版本最新版本。

docker 架构图：
<img src="..\images\docker&k8s\docker-architecture.png"/>

docker 架构是一个客户端-服务端架构模式：

> **Client** : 客户端，在它上面一般执行 docker 的各种命令，比如 `docker run` 。
> **DOCKER HOST** : docker 的服务端，里面的守护进程 Docker daemon 接收并执行来自客户端的请求命令。
> Containers - 容器，Images - 各种镜像。
> 用 docker 命令运行一个镜像，这个镜像就变成了一个运行的容器。这 2 者之间的关系相当于面向对象编程中的类和实例之间的关系。
> **Registry** ：存放镜像仓库 。比如里面存放的 Redis 镜像，Nginx 镜像。我们制作的镜像文件都可以存放在这里。

## 二、常用命令[#](https://www.cnblogs.com/jiujuan/p/13758351.html#3459801374)

### 2.1 镜像命令[#](https://www.cnblogs.com/jiujuan/p/13758351.html#147659297)

下载镜像

> docker pull nginx //下载镜像
> docker pull nginx:v1.4.14

运行镜像

> docker run -it nginx //运行镜像
> docker run --name webserver -d -p 80:80 nginx // 运行镜像，--name 给镜像取一个名字 -d 后台运行模式 -p 镜像端口映射

镜像帮助信息

> docker image --help // 获取镜像操作的命令帮助

搜索镜像

> //docker search ... 镜像搜索
>
> docker search nginx // 搜索nginx镜像

查看镜像历史信息

> docker image history nginx // 查看镜像历史

查看镜像详细信息

> docker image inspect nginx //查看镜像详细信息

列出镜像

> docker images //列出镜像
> docker image ls //列出镜像
> docker image ls -a //docker image ls 命令列表中只显示顶层镜像。`-a` 参数可以显示包括中间层在内的所有镜像
> docker image ls nginx //根据仓库名列出镜像
> docker image ls nginx:v2 //根据某个特定标签列出镜像

查看镜像、容器、数据卷大小

> docker system df //查看镜像、容器、数据卷所占的空间大小

删除镜像

> docker image rm centos:v7 // 按照标签tag删除镜像，也可以根据image id删除
>
> //**提示**：删除镜像的前提是该镜像没有运行成容器；docker image rm 等同docker rmi命令；如果需要强行删除已经运行为容器的镜像，可以使用-f选项来指定；
>
> docker image rm af34 //根据ID删除镜像，一般取前3个字符或以上就可以了
>
> docker image rm $(docker image ls -q nginx) //过滤删除命令。删除所有仓库名为 `nginx` 的镜像
> docker image rm $(docker image ls -q -f before=nginx:v2) //-f 强制删除 -q 查询，这句命令：删除所有在 `nginx:v2` 之前的镜像

给镜像打tag

> docker tag centos centos:v6.7 //给镜像打标签 tag
>
> //**提示**：docker image tag 和docker tag 等同；都是用于给镜像打标签；打标签的意思类似给镜像取别名的意思；

### 2.2 容器命令[#](https://www.cnblogs.com/jiujuan/p/13758351.html#479380776)

新建并启动容器

> docker run ... //新建并启动容器命令
> docker run centos:v7 /bin/echo 'Hello World' //输出一个 'Hello World'之后终止容器
> docker run -t -i centos:v7 /bin/bash // 启动一个bash端，允许用户进行交互，`-t` 让Docker分配一个伪终端（pesudo-tty）并绑定到容器的标准输入上，`-i` 让容器的标准输入保持打开
> docker run -d centos:v7 /bin/sh -c "while true;do echo 11;sleep 1; done" //-d 在后台运行
> //提示：可以用 docker logs 查看输出的结果

查看容器本身的信息

> docker container ls // 查看容器信息
> docker container ls -a

查看容器日志信息

> docker container logs //获取容器的log输出信息
> docker container logs [container ID or NAMES]

终止和启动容器

> docker container stop // 终止一个运行中的容器
> docker container ls -a // 可以查看容器终止的状态信息
> docker container start //启动一个已经终止的容器
> docker container restart // 将一个运行态的容器终止，然后重新启动

进入容器 docker attach 或 docker exec

> docker run -dit centos
> docker container ls
> docker attach a12f // a12f 容器ID的前4个字符

> docker run -dit centos
> docker container ls
> docker exec -it 34af bash // -i 这个参数没有分配伪终端但是保持了标准输出，-t 分配一个伪终端

> docker exec --help // 查看更多exec的参数使用方法

导出容器

> docker container ls -a
> docker export r1dfdere112122 > centos.tar //r1dfdere112122 容器ID

导入容器快照

> cat centos.tar | docker import - test/centos:v1.0 //本地导入
> docker import URL //通过指定的url导入

删除容器
docker container rm 删除一个处于终止状态的容器

> docker container rm centos
> //**提示**：如果要删除一个运行中的容器，需要添加参数 `-f` 参数。 这时，docker 会发送 `SIGKILL` 信号给容器。

清理所有终止状态的容器

> docker container ls -a // 查看所有已经创建包括终止状态的容器
> docker container prune // 清除所有的容器

### 2.3 数据卷[#](https://www.cnblogs.com/jiujuan/p/13758351.html#3654609248)

创建数据卷

> docker volume create first-volume

查看所有的数据卷

> docker volume ls

查看某一数据卷信息

> docker volume inspect first-volume

挂载数据卷的容器

> docker run -d -P \
> --name web \
> \#-v first-volume:/webapp \ // -v 参数挂载
> --mount source=first-volumn, target=/webapp \
> training/webapp \
> python app.py
>
> //创建一个名为web的容器，并挂载数据卷到容器的 /webapp 目录

查看数据卷信息

> docker inspect web

删除数据卷

> docker volumn rm first-volumn

数据卷 是被设计用来持久化数据的，它的生命周期独立于容器，Docker不会在容器被删除后自动删除数据库，也不存在垃圾回收机制处理没有任何容器引用的数据卷。
如果要在删除容器时候删除数据就，可以在删除容器时候使用 docker rm -v 这个命令

清除数据卷

> docker volume prune // 清除无主的数据卷

--mount 可以挂载一个本地主机目录导容器中去

> docker run -d -P \
> --name web \
> --mount type=bind, source=/src/webapp, target=/opt/webapp \
> training/webapp \
> python app.py
>
> //上面的命令就是挂在主机的 /src/webapp 目录到容器的 /opt/webapp 目录。

### 2.4 网络[#](https://www.cnblogs.com/jiujuan/p/13758351.html#2059605046)

#### 端口绑定[#](https://www.cnblogs.com/jiujuan/p/13758351.html#3984835988)

参数：
-P（大写） ：Docker会随机映射一个 49000-49900 的端口到内部容器开发的网络端口
-p（小写） ： 自定义端口映射

映射随机端口：

> docker run -d -P training/webapp python app.py
>
> docker container ls -l
> CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
> bc533791f3f5 training/webapp:latest python app.py 5 seconds ago Up 2 seconds 0.0.0.0:49155->5000/tcp nostalgic_morse
>
> //本地主机的 49155 被映射到了容器的5000端口。此时访问本机的49155端口即可访问容器内web应用提供的界面

查看应用信息

> docker logs -f nostalgic_morse

自定义端口映射

> docker run -d -p 5000:5000 training/webapp python app.py
> // 默认把本机所有地址的映射到5000端口上

自定义指定地址的端口：

> docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py

自定义地址的任意端口

> docker run -d -p 127.0.0.1::5000 training/webapp python app.py

指定udp端口

> docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py

查看映射端口配置 docker port

> docker port nostalgic_morse 5000

绑定多个端口 -p

> docker run -d \
> -p 5000:5000 \
> -p 3000:80 \
> training/webapp \
> python app.py

#### 容器互连[#](https://www.cnblogs.com/jiujuan/p/13758351.html#1318718519)

一般使用docker，会使用 --link 来使容器互连。
现在则可以使用 -d 参数来指定docker的网络类型。docker网络类型有 `bridge`，`overlay`。

创建新的docker网络

> docker network create -d bridge first-net

连接容器

> docker run -it --rm --name busybox1 --network first-net busybox sh
> // 打开新的终端，在运行一个容器并加入 first-net 网络
> docker run -it --rm --name busybox2 --network first-net busybox sh
> // 在打开一个终端查看容器信息
> docker container ls
>
> CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
> b47060aca56b busybox "sh" 11 minutes ago Up 11 minutes busybox2
> 8720575823ec busybox "sh" 16 minutes ago Up 16 minutes busybox1

> // 用ping命令证明容器busybox1和容器busybox2建立了互连关系
> // 进入 busybox1 容器，然后输入以下命令
>
> ping buxybox2
>
> PING busybox2 (172.19.0.3): 56 data bytes
> 64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.072 ms
> 64 bytes from 172.19.0.3: seq=1 ttl=64 time=0.118 ms

## 三、参考[#](https://www.cnblogs.com/jiujuan/p/13758351.html#1950076098)

- [docker_practice](https://yeasy.gitbook.io/docker_practice/)

作者：九卷 (公众号：九卷技术录)

出处：https://www.cnblogs.com/jiujuan/p/13758351.html

版权：本文采用「[署名-非商业性使用-相同方式共享 by nc nd 4.0 国际](https://creativecommons.org/licenses/by-nc-nd/4.0/)」知识共享许可协议进行许可。

【好技术成就你我】

分类: [[04-1\]docker-k8s云原生](https://www.cnblogs.com/jiujuan/category/1857746.html)

标签: [docker](https://www.cnblogs.com/jiujuan/tag/docker/)

0

0

[« ](https://www.cnblogs.com/jiujuan/p/13732880.html)上一篇： [消息队列常见问题分析](https://www.cnblogs.com/jiujuan/p/13732880.html)
[» ](https://www.cnblogs.com/jiujuan/p/13762969.html)下一篇： [微服务架构学习与思考(05)：微服务架构适用场景分析](https://www.cnblogs.com/jiujuan/p/13762969.html)

posted @ 2020-10-01 19:38 [九卷](https://www.cnblogs.com/jiujuan) 阅读(823) 评论(0) [编辑](https://i.cnblogs.com/EditPosts.aspx?postid=13758351) [收藏](javascript:void(0)) [举报](javascript:void(0))