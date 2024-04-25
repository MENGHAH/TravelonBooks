# docker相关
## 1. docker安装
> [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html)

安装相关依赖
```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

配置为阿里云：
```shell
sudo yum-config-manager \ --add-repo \ https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
安装最新版本
```shell
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

安装指定版本
```shell
查看版本：sudo yum list docker-ce --showduplicates | sort -r 
安装指定版本：sudo yum install docker-ce-20.10.0 docker-ce-cli-20.10.0 containerd.io
```

查看docker版本

```shell
docker version
```

启动docker并设置开机自启动

```shell
sudo systemctl start docker 
sudo systemctl enable docker
```

停止docker：systemctl stop docker

重启docker：service docker restart 

登录已运行的容器：docker exec -it ID /bin/sh   

## 2. docker卸载
> [卸载并重装docker](https://blog.csdn.net/qq_37171817/article/details/107760339)

查看已安装的docker
```shell
yum list installed|grep docker
```

删除docker
```shell
yum -y remove docker.x86_64 docker-client.x86_64 docker-common.x86_64
```

删除已有镜像、容器以及配置文件等
```shell
rm -rf  /var/lib/docker
```
## 3. docker常用指令

> [Docker常用命令总结](https://www.cnblogs.com/jiujuan/p/13758351.html)

Docker相关指令 
- 查看当前仓库支持的docker版本： yum list docker-ce --showduplicates | sort -r
- 降低docker版本到18.06.3.ce-3.el7： yum downgrade --setopt=obsoletes=0 -y docker-ce-20.10.0-3.el7 docker-ce-cli-20.10.0-3.el7 containerd.io
- 卸载docker：
  安装docker sudo yum install -y yum-utils \ device-mapper-persistent-data \ lvm2
  设置为阿里云：sudo yum-config-manager \ --add-repo \ https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
  查看版本：sudo yum list docker-ce --showduplicates | sort -r 安装指定版本：sudo yum install docker-ce-20.10.0 docker-ce-cli-20.10.0 containerd.io

## 4. docker构建私有仓库

拉取registry

```shell
docker -D pull registry
docker -D pull hell-world(该操作是为了后续测试方便，所以先把hello-world镜像下载下来)
```

启动registry

```shell
docker run -d -p 5000:5000 --name="bcmt-registry" --restart=always -v /root/docker/registry/:/var/lib/registry/ registry 

docker ps -a // 可以看到bcmt-registry已经启动
```

配置本地hosts

```shell
vim /etc/hsots

添加如下映射关系：
本地IP bcmt-registry

添加完成后保存退出
```

配置docker的daemon.json

```shell
vim /etc/docker/daemon.json

添加如下内容：
"insecure-registries": ["本地IP:5000", "bcmt-registry:5000"]

保存退出并重启docker
sudo systemctl daemon-reload
sudo systemctl docker restart
```

push镜像到私有仓库bcmt-registry

```shell
首先对目标镜像重命名
docker tag hello-world:latest bcmt-registry:5000/hello-world:latest

push镜像
docker push bcmt-registry:5000/hello-world:latest
```



<font color=red>注意: 在push镜像时，如果要使用别名（bcmt-registry:5000）而非本地IP（本地ip:5000），一定要在/etc/hosts中配置映射关系。在docker配置了代理后，也需要在代理中去除别名的路由代理</font>



查看私有仓库里的镜像

```shell
curl 127.0.0.1:5000/v2/_catalog/
```

删除私有仓库里的镜像



# K8S相关

## 1. K8S部署

1. 查看 ip
```shell
ip addr 
```
2. 修改主机名称
```shell
hostnamectl set-hostname k8s-master && bash
```
3. 添加host
```shell
cat > /etc/hosts << EOF
10.0.4.16 k8s-master

127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6

EOF
```
4. 关闭防火墙
```shell
systemctl stop firewalld
systemctl disable firewalld
```
5. 关闭selinux
```shell
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=disabled/' /etc/selinux/config
```
6. 关闭swap
```shell
# 临时
swapoff -a 
# 永久
sed -i 's/.*swap.*/#&/' /etc/fstab
```
7. 配置 Iptables 链路
```shell
cat > /etc/sysctl.d/k8s.conf <<EOF
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp_keepalive_intvl = 30
net.ipv4.tcp_keepalive_probes = 10
EOF

即时生效：sysctl --system 
```
8.时间同步
```shell
yum install ntpdate -y 
ntpdate time.windows.com
```
9. 添加 K8S yum 源
```shell
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
10. 安装 kubeadm、kubelet、kubectl
```shell
yum -y install kubelet-1.25.0 kubectl-1.25.0 kubeadm-1.25.0
systemctl enable kubelet
```
11. 安装 k8s
```shell
kubeadm init \
--apiserver-advertise-address 10.0.76.120 \ 
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.23.5 \
--service-cidr 10.96.0.0/12 \
--pod-network-cidr 10.244.0.0/16 \
--ignore-preflight-errors all \
--token-ttl 0
```

参数解释：
apiserver-advertise-address：设定为本地IP
kubernetes-version: 根据自身安装版本设定

**init执行太慢或卡住的解决方案：**

1. 执行关闭swap后再次执行init


2. 按照以下经验贴执行

   > - [【K8S集群安装二】K8S集群安装步骤](https://blog.csdn.net/jiangjun_dao519/article/details/126278502?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-126278502-blog-130469200.235%5Ev38%5Epc_relevant_sort_base3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-126278502-blog-130469200.235%5Ev38%5Epc_relevant_sort_base3&utm_relevant_index=1)
   >
   > - [Centos7 安装部署Kubernetes(k8s)集群](https://www.cnblogs.com/renshengdezheli/p/16686769.html)

   上述经验贴中介绍如何通过docker手动拉取init过程中需要的镜像以实现init的过程。

   ```shell
   1. docker pull coredns/coredns
   2. docker tag coredns/coredns:latest
   3. docker tag coredns/coredns:latest registry.aliyuncs.com/google_containers/coredns/coredns:v1.8.0
   ```

   

3. 检查docker是否需要配置代理以访问镜像源

   如果docker无法正确的网络连接， 会出现“Failed to pull images” 的问题。

   > 在这个过程中也可能设计修改镜像源的问题，可以参考下帖
   >
   > - [使用清华大学的 Docker 镜像源](https://blog.csdn.net/WangAnJiao/article/details/131661953)
   >
   > ```shell
   > sudo vim /etc/docker/daemon.json（如果没有此文件就新建一个）
   > 添加
   > {
   >  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn","https://docker.mirrors.tuna.tsinghua.edu.cn"]
   > }
   > 
   > 重启docker
   > sudo service docker restart
   > ```

12. 安装calico插件

```shell
grep -w "image" calico.yaml

for i in calico/cni:v3.25.0 calico/pod2daemon-flexvol:v3.25.0 calico/node:v3.25.0 calico/kube-controllers:v3.25.0 ; do docker pull $i ; done

kubectl apply -f calico.yaml


```



## 2. worker重置

1. 在准备移除的 worker 节点上执行

```perl
kubeadm reset -f
```

2. 在 master 节点上执行

```csharp
kubectl get nodes -o wide
```

3. 删除worker节点，在 master 节点上执行

```cpp
kubectl delete node demo-worker-x-x
```

> 将 demo-worker-x-x 替换为要移除的 worker 节点的名字
> worker 节点的名字可以通过在节点master上执行 kubectl get nodes 命令获得
