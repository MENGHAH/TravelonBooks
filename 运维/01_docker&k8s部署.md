# docker相关
## 1. docker安装
参考文档：https://www.runoob.com/docker/centos-docker-install.html

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

## 2. docker卸载
删除旧docker相关内容
```shell
sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
sudo yum remove docker-c
```

删除镜像、容器、配置文件等
```shell
rm -rf /var/lib/docker
```
## 3. docker常用指令
Docker相关指令 
- 查看当前仓库支持的docker版本： yum list docker-ce --showduplicates | sort -r
- 降低docker版本到18.06.3.ce-3.el7： yum downgrade --setopt=obsoletes=0 -y docker-ce-20.10.0-3.el7 docker-ce-cli-20.10.0-3.el7 containerd.io
- 停止docker：systemctl stop docker
- 启动docker：systemctl start docker （查看版本：docker version）
- 重启docker：service docker restart 
- 设置开机启动：systemctl enable docker
卸载docker：
安装docker sudo yum install -y yum-utils \ device-mapper-persistent-data \ lvm2
设置为阿里云：sudo yum-config-manager \ --add-repo \ https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
查看版本：sudo yum list docker-ce --showduplicates | sort -r 安装指定版本：sudo yum install docker-ce-20.10.0 docker-ce-cli-20.10.0 containerd.io
# K8S部署
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

init执行太慢或卡住解决方案：
> 1.	https://blog.csdn.net/jiangjun_dao519/article/details/126278502?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-126278502-blog-130469200.235%5Ev38%5Epc_relevant_sort_base3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-126278502-blog-130469200.235%5Ev38%5Epc_relevant_sort_base3&utm_relevant_index=1
> 2.	https://www.cnblogs.com/renshengdezheli/p/16686769.html

1. 执行关闭swap后再次执行init


2. 安装上帖子中方案执行
- 修改docker镜像源
https://www.jianshu.com/p/dfd30e94b517
https://blog.csdn.net/WangAnJiao/article/details/131661953

```shell
sudo vim /etc/docker/daemon.json（如果没有此文件就新建一个）
添加
{
 "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
也可以使用清华源
{
 "registry-mirrors": ["https://docker.mirrors.tuna.tsinghua.edu.cn"]
}
重启docker
sudo service docker restart
测试
docker search nginx
```
- 拉取依赖
```shell
docker pull coredns/coredns
docker tag coredns/coredns:latest
docker tag coredns/coredns:latest registry.aliyuncs.com/google_containers/coredns/coredns:v1.8.0
```



