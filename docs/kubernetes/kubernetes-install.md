![](../imgs/k8s/202011071500.jpeg)
#### K8S集群安装部署
<hr>

##### 1. 虚拟服务器的构建
这里需要用到5台虚拟服务器，其中三台是节点服务器。另外两台分别是装有 Harbor 私有仓库的服务器和安装 koolshare 软路由的服务器：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608022802252.png)	
<hr>

##### 2. 安装软路由
指定使用老毛桃镜像，启动软路由服务器：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608010322321.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

启动后选择第一项，启动 Win10 X64 PE：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608010514866.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

将光盘换成封装了软路由的文件：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608020246891.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

将软路由的img文件写入磁盘中：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608020653665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

取消设备状态下的选项，将镜像文件从光驱中弹出：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608020941584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

关机然后为这台服务器添加一块新的虚拟网卡，将该网卡的网络连接方式设置为NET模式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608021915440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

>服务器一端使用NET网络连接方式可以连接本地主机的网络，共享本地主机的IP地址，通过本机主机上网。服务器的另外一端通过仅主机网络与 K8S 节点相连。koolshare上会运行一个叫 ssr 的插件，使用这个 ssr 插件可以通过本地主机实现 Surfing Scientifically，让 K8S 集群拥有访问Google、连接镜像服务器的能力。这样就可以直接在网络上进行初始化。

编辑虚拟网络，使用本地 DHCP 服务分发 IP 地址：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200608023521508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 3. 系统初始化
在大型环境下建议通过DNS的方式使各个节点的主机和IP能够相互解析，小型环境下直接修改hosts文件就可以了，因为如果DNS挂掉集群就没有作用了。系统初始化是三台主机都需要做的。

设置 master 主机和两台 node 主机的名称，设置完成后重启才能生效：
```python
[root@localhost ~]$ hostnamectl set-hostname k8s-master-01
```
配置master的hosts文件并把配置好的文件传输给两个两台node主机：
```python
[root@k8s-master-01 ~]$ vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.0.0.2 k8s-master-01
10.0.0.3 k8s-node-01
10.0.0.4 k8s-node-02
[root@k8s-master-01 ~]$ scp /etc/hosts root@k8s-node-01:/etc
[root@k8s-master-01 ~]$ scp /etc/hosts root@k8s-node-02:/etc
```
安装依赖包，以 master 为例：
```python
[root@k8s-master-01 ~]$ yum install -y conntrack ntpdate ntp ipvsadm ipset jq iptables curl sysstat libseccomp wgetvimnet-tools git
```
关闭 firewalld 防火墙，防火墙设为 Iptables，还要设置空规则并保存。然后关闭 firewalld：
```python
[root@k8s-master-01 ~]$ yum -y install iptables-services && systemctl start iptables && systemctl  enable iptables && iptables -F && service iptables save
[root@k8s-master-01 ~]$ systemctl stop firewalld && systemctl disable firewalld
```
关闭虚拟内存和 SELINUX：
```python
[root@k8s-master-01 ~]# swapoff -a && sed -i '/swap/ s/^\(.*\)$/#\1/g' /etc/fstab
[root@k8s-master-01 ~]# setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```
>安装 k8s 的时候初始化的时候会检测 swap 分区是否关闭，如果开启了虚拟内存，容器 pod 就有可能在虚拟内存中运行，大大降低工作效率。会要求服务器强制关闭虚拟内存，虽然可以排除这个报错，但是建议还是要关闭。

调整内核参数，<font>关闭 ipv6 的协议和开启网桥模式比较重要，是安装 k8s 必须要选择的</font>。其它几条是优化方案：
```python
[root@k8s-master-01 ~]$ cat > kubernetes.conf <<EOF
net.bridge.bridge-nf-call-iptables=1
net.bridge.bridge-nf-call-ip6tables=1
net.ipv4.ip_forward=1
net.ipv4.tcp_tw_recycle=0
vm.swappiness=0 # 禁止使用 swap 空间，只有当系统 OOM 时才允许使用它
vm.overcommit_memory=1 # 不检查物理内存是否够用
vm.panic_on_oom=0 # 开启 OOM
fs.inotify.max_user_instances=8192
fs.inotify.max_user_watches=1048576
fs.file-max=52706963
fs.nr_open=52706963
net.ipv6.conf.all.disable_ipv6=1
net.netfilter.nf_conntrack_max=2310720
EOF
[root@k8s-master-01 ~]$ ls
anaconda-ks.cfg  kubernetes.conf
```
将优化的文件放在 <kbd>/etc/sysctl.d/</kbd> 目录下，保证开机的时候可以被调用。然后手动刷新，立刻生效：
```python
[root@k8s-master-01 ~]$ cp kubernetes.conf /etc/sysctl.d/kubernetes.conf
[root@k8s-master-01 ~]$ sysctl -p /etc/sysctl.d/kubernetes.conf 
sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-iptables: 没有那个文件或目录
sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-ip6tables: 没有那个文件或目录
net.ipv4.ip_forward = 1
net.ipv4.tcp_tw_recycle = 0
vm.swappiness = 0 # 禁止使用 swap 空间，只有当系统 OOM 时才允许使用它
vm.overcommit_memory = 1 # 不检查物理内存是否够用
vm.panic_on_oom = 0 # 开启 OOM
fs.inotify.max_user_instances = 8192
fs.inotify.max_user_watches = 1048576
fs.file-max = 52706963
fs.nr_open = 52706963
net.ipv6.conf.all.disable_ipv6 = 1
net.netfilter.nf_conntrack_max = 2310720
```
调整系统的时区，如果安装的时候没有选择 Asia/Shanghai 主机都需要设置下时区：
```python
[root@k8s-master-01 ~]$ timedatectl set-timezone Asia/Shanghai
[root@k8s-master-01 ~]$ timedatectl set-local-rtc 0
[root@k8s-master-01 ~]$ systemctl restart rsyslog
[root@k8s-master-01 ~]$ systemctl restart crond
```
关闭系统不需要的服务，如邮件服务：
```python
[root@k8s-master-01 ~]$ systemctl stop postfix && systemctl disable postfix
```
设置 rsyslog 和 systemd journald：
```python
# 创建持久化保存日志的目录
[root@k8s-master-01 ~]$ mkdir /var/log/journal
# 创建配置文件存放目录
[root@k8s-master-01 ~]$ mkdir /etc/systemd/journald.conf.d
# 创建配置文件
[root@k8s-master-01 ~]$ cat > /etc/systemd/journald.conf.d/99-prophet.conf <<EOF
[Journal]
# 持久化保存到磁盘
Storage=persistent
# 压缩历史日志
Compress=yes
SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000
# 最大占用空间 10G
SystemMaxUse=10G
# 单日志文件最大 200M
SystemMaxFileSize=200M
# 日志保存时间 2 周
MaxRetentionSec=2week
# 不将日志转发到 syslog
ForwardToSyslog=no
EOF
```
重启systemd journald：
```python
[root@k8s-master-01 ~]$ systemctl restart systemd-journald
```
>在 CentsOS7 之后引导方式改为了 ctud，会有两个日志系统在同时工作。默认是 rsyslogd，另外一个是 systemd journald。使用 systemd journald 的方案更好以下，所以需要把 systemd journald 改为默认。

CentOS 7.x 系统自带的 3.10.x 内核存在一些 Bugs，导致运行的 Docker、Kubernetes 不稳定。所以需要去安装4.44内核版本，可以有效提高Kubernetes的稳定性。
```python
[root@k8s-master-01 ~]$ rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```
安装完成后检查 <font>/boot/grub2/grub.cfg</font> 中对应内核 menuentry 中是否包含 initrd16 配置，如果没有，再安装一次：
```python
[root@k8s-master-01 ~]$ yum --enablerepo=elrepo-kernel install -y kernel-lt
```
设置开机从新内核启动（默认启动的内核），然后重启后查看是否是新内核启动：
```python
[root@k8s-master-01 ~]$ grub2-set-default 'CentOS Linux (4.4.226-1.el7.elrepo.x86_64) 7 (Core)'
[root@k8s-master-01 ~]$ reboot
[root@k8s-master-01 ~]$ uname -a
Linux k8s-master-01 4.4.226-1.el7.elrepo.x86_64 #1 SMP Tue Jun 2 09:51:15 EDT 2020 x86_64 x86_64 x86_64 GNU/Linux
```
<hr>

##### 4. 开启IPVS的前置条件
kube-proxy 主要解决 svc 与 pod 之间的调度关系，IPVS 的调度方式可以极大增加访问效率，所以这种方式是非常必备的方式。开启 IPVS 的前置条件，加载 netfilter 模块，三台主机都需要做下面的操作。
```python
[root@k8s-master-01 ~]$ modprobe br_netfilter
```
创建引导 IPVS 相关依赖（模块）加载的文件：
```python
[root@k8s-master-01 ~]$ cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
```
赋予 755 权限然后执行这个文件：
```python
[root@k8s-master-01 ~]$ chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules
```
查看模块是否被引导：
```python
[root@k8s-master-01 ~]$ lsmod | grep -e ip_vs -e nf_conntrack_ipv4
nf_conntrack_ipv4      20480  0 
nf_defrag_ipv4         16384  1 nf_conntrack_ipv4
ip_vs_sh               16384  0 
ip_vs_wrr              16384  0 
ip_vs_rr               16384  0 
ip_vs                 147456  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack          114688  2 ip_vs,nf_conntrack_ipv4
libcrc32c              16384  2 xfs,ip_vs
```
<hr>

##### 5. 安装Docker
三台主机都需要安装 Docker，安装 Docker 软件的依赖：
```python
[root@k8s-master-01 ~]$ yum install -y yum-utils device-mapper-persistent-data lvm2
```
导入阿里源 docker-ce 镜像仓库：
```python
[root@k8s-master-01 ~]$ yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
更新系统，然后安装 docker-ce：
```python
[root@k8s-master-01 ~]$ yum update -y && yum install -y docker-ce
```
安装完成之后，将内核版本重新设置为新版本，然后 <font>重启</font>：
```python
[root@k8s-master-01 ~]$ grub2-set-default 'CentOS Linux (4.4.226-1.el7.elrepo.x86_64) 7 (Core)'
[root@k8s-master-01 ~]$ reboot
```
>配置使用新内核启动被修改了！

启动docker并且设置为开机自启：
```python
[root@k8s-master-01 ~]$ systemctl start docker && systemctl enable docker
```
配置daemon，daemon.json相当于子配置文件：
```python
[root@k8s-master-01 ~]$ cat > /etc/docker/daemon.json <<EOF
{
	"exec-opts": ["native.cgroupdriver=systemd"],
	"log-driver": "json-file",
	"log-opts": {
		"max-size": "100m"  
	}
}
EOF
```
创建目录用来存放 Docker 生成的子配置文件：
```python
[root@k8s-master-01 ~]$ mkdir -p /etc/systemd/system/docker.service.d
```
重新读取配置文件，然后重启Docker，开机自启动：
```python
[root@k8s-master-01 ~]$ systemctl daemon-reload && systemctl restart docker && systemctl enable docker
```
<hr>

##### 6. 安装Kubeadm
三台主机都 <font>需要安装 Kubeadm</font>，添加 Kubeadm 的 yum 仓库：
```python
[root@k8s-master-01 ~]$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
安装 Kubeadm、kubectl 和 kubelet：
```python
[root@k8s-master-01 ~]$ yum -y install kubeadm-1.15.1 kubectl-1.15.1 kubelet-1.15.1
```
设置kubelet开机自启：
```python
[root@k8s-master-01 ~]$ systemctl enable kubelet.service
```
>kubelet需要和容器接口交互，启动容器。而我们的K8S通过kubeadn安装之后都是以pod的方式存在，底层是以容器的方式运行。

<hr>

##### 7. 初始化节点
Kubeadm 在初始化 K8S 集群的时候会从 GCE 谷歌的云服务器里拉去所需要的镜像，所以需要 Surfing Scientifically。<font>如果有SSR可以通过软路由的方式让我们的所有机器 Surfing Scientifically</font>。由于没有SSR的原因，所以这里把镜像打包成压缩文件来安装。

从本地把 kubeadm-basic.images.tar.gz 镜像文件上传到三台服务器：
```python
$ scp kubeadm-basic.images.tar.gz root@10.0.0.2:/root/
$ scp kubeadm-basic.images.tar.gz root@10.0.0.3:/root/
$ scp kubeadm-basic.images.tar.gz root@10.0.0.4:/root/
```
解压镜像文件：
```python
[root@k8s-master-01 ~]$ tar zxvf kubeadm-basic.images.tar.gz
kubeadm-basic.images/
kubeadm-basic.images/coredns.tar
kubeadm-basic.images/etcd.tar
kubeadm-basic.images/pause.tar
kubeadm-basic.images/apiserver.tar
kubeadm-basic.images/proxy.tar
kubeadm-basic.images/kubec-con-man.tar
kubeadm-basic.images/scheduler.tar
```
使用 shell 脚本把子镜像导入：
```python
[root@k8s-master-01 ~]$ vim load-images.sh 
```
<kbd>load-images.sh</kbd>
```python
#!/usr/bin/bash
ls /root/kubeadm-basic.images > /tmp/image-list.txt

cd /root/kubeadm-basic.images

for i in $( cat /tmp/image-list.txt )

do
        docker load -i $i
done

rm -rf /tmp/image-list.txt
```
给脚本赋予可以所有用户可以执行的权限，然后执行这个脚本，<font>非主节点的两台主机执行这个脚本之后下面的所有作不再执行，直到网络部署部分加入子节点</font>：
```python
[root@k8s-master-01 ~]$ chmod a+x load-images.sh 
[root@k8s-master-01 ~]$ ./load-images.sh 	
```
显示 init-default 默认的初始化文件打印到 <kbd>kubeadm-config.yaml</kbd> 文件中，这样就获得了 kubeadm 默认的初始化模板：
```python
[root@k8s-master-01 ~]$ kubeadm config print init-defaults > kubeadm-config.yaml
```
修改服务器的节点地址、kubernets版本配置，增加podSubnet和修改默认调度方式改为IPVS的字段（文件#号标识处需要删除）：
```python
[root@k8s-master-01 ~]$ vim kubeadm-config.yaml 
apiVersion: kubeadm.k8s.io/v1beta2
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 10.0.0.2	# 需要修改当前节点
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: k8s-node-01
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta2
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: k8s.gcr.io
kind: ClusterConfiguration
kubernetesVersion: v1.15.1		# 需要修改kubernetes的版本
networking:
  dnsDomain: cluster.local
  podSubnet: "10.244.0.0/16"	# 新添加podSubnet字段
  serviceSubnet: 10.96.0.0/12
scheduler: {}    				# 默认的调度方式改为IPVS
---
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
```
指定从哪个 yaml 文件安装以及制度颁发证书和把所有信息写入到 <kbd>kubeadm-init.log</kbd> 中，<font>可用的CPU不少于2个，一核心2个处理器数量也是可以的</font>：
```python
[root@k8s-node-01 ~]$ kubeadm init --config=kubeadm-config.yaml --experimental-upload-certs | tee kubeadm-init.log
```
>1.13 版之后才 Kubeadm 开始支持高可用，可以让其它子结点自动颁发证书。

Kubernetes 初始化成功之后，在当前家目录下创建.kube 目录用来保存连接配置，认证文件 config 也会被保存在这个目录中：
```python
[root@k8s-master-01 ~]$ mkdir -p $HOME/.kube
[root@k8s-master-01 ~]$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@k8s-master-01 ~]$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
>kubecrl 需要采用hps协议与 kuber api 交互，交互的时候产生的缓存会保存在.kube目录下。

查看当前有哪些节点：
```python
[root@k8s-master-01 ~]$ kubectl get node
NAME          STATUS     ROLES    AGE   VERSION
k8s-node-01   NotReady   master   18m   v1.15.1
```
<hr>

##### 8. 网络部署
创建 flannelc 网络插件，需要把子结点加入到主节点。master 主机中主节点操作如下：
```python
[root@k8s-master-01 ~]$ mkdir install-k8s
[root@k8s-master-01 ~]$ mv kubeadm-init.log kubeadm-config.yaml install-k8s/
[root@k8s-master-01 ~]$ cd install-k8s/
[root@k8s-master-01 install-k8s]$ mkdir core
[root@k8s-master-01 install-k8s]$ mv * core/
[root@k8s-master-01 install-k8s]$ mkdir plugin
[root@k8s-master-01 install-k8s]$ cd plugin/
[root@k8s-master-01 plugin]$ mkdir flannel
[root@k8s-master-01 flannel]$ wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
[root@k8s-master-01 flannel]$ kubectl create -f kube-flannel.yml 
[root@k8s-master-01 flannel]$ kubectl get pod -n kube-system
NAME                                  READY   STATUS    RESTARTS   AGE
coredns-5c98db65d4-scccx              1/1     Running   0          25m
coredns-5c98db65d4-sppjh              1/1     Running   0          25m
etcd-k8s-node-01                      1/1     Running   0          24m
kube-apiserver-k8s-node-01            1/1     Running   0          24m
kube-controller-manager-k8s-node-01   1/1     Running   0          24m
kube-flannel-ds-amd64-tbkgv           1/1     Running   0          59s
kube-proxy-bp2bd                      1/1     Running   0          25m
kube-scheduler-k8s-node-01            1/1     Running   0          24m
[root@k8s-master-01 flannel]$ kubectl get node
NAME          STATUS   ROLES    AGE   VERSION
k8s-node-01   Ready    master   25m   v1.15.1
[root@k8s-master-01 ~]$ ip a
6: flannel.1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default 
    link/ether 5e:58:2e:f9:37:39 brd ff:ff:ff:ff:ff:ff
    inet 10.244.0.0/32 scope global flannel.1
```
查看日志：
```python
[root@k8s-master-01 core]$ cat kubeadm-init.log 
```
将子结点加入进来：
```python
[root@k8s-node-01 ~]$ kubeadm join 10.0.0.2:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:930e41e2c62450d2cd3e3cfc609cf2d43242bf35e73e48b8555797245c9c9e7e
```
master主机上查看节点信息：
```python
[root@k8s-master-01 ~]$ kubectl get nodes
[root@k8s-master-01 core]# kubectl get nodes
NAME            STATUS   ROLES    AGE   VERSION
k8s-master-01   Ready    master   82m   v1.15.1
k8s-node-01     Ready    <none>   17m   v1.15.1
```
master主机上查看详细的节点信息：
```python
[root@k8s-master-01 ~]$ kubectl get nodes -o wide
NAME            STATUS   ROLES    AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
k8s-master-01   Ready    master   9h      v1.15.1   10.0.0.2      <none>        CentOS Linux 7 (Core)   4.4.226-1.el7.elrepo.x86_64   docker://19.3.11
k8s-node-01     Ready    <none>   8h      v1.15.1   10.0.0.3      <none>        CentOS Linux 7 (Core)   4.4.226-1.el7.elrepo.x86_64   docker://19.3.11
k8s-node-02     Ready    <none>   5h22m   v1.15.1   10.0.0.4      <none>        CentOS Linux 7 (Core)   4.4.226-1.el7.elrepo.x86_64   docker://19.3.11
```
监视的方式查看节点信息，实时查看有没有变化：
```python
[root@k8s-master-01 core]$ kubectl get pod -n kube-system -w
```
查看更详细的节点信息：
```python
[root@k8s-master-01 core]$ kubectl get pod -n kube-system -o wide
NAME                                    READY   STATUS    RESTARTS   AGE     IP            NODE            NOMINATED NODE   READINESS GATES
coredns-5c98db65d4-r6sbc                1/1     Running   4          9h      10.244.0.15   k8s-master-01   <none>           <none>
coredns-5c98db65d4-zq2xf                1/1     Running   4          9h      10.244.0.14   k8s-master-01   <none>           <none>
etcd-k8s-master-01                      1/1     Running   6          9h      10.0.0.2      k8s-master-01   <none>           <none>
kube-apiserver-k8s-master-01            1/1     Running   6          9h      10.0.0.2      k8s-master-01   <none>           <none>
kube-controller-manager-k8s-master-01   1/1     Running   6          9h      10.0.0.2      k8s-master-01   <none>           <none>
kube-flannel-ds-amd64-gd55v             1/1     Running   2          8h      10.0.0.3      k8s-node-01     <none>           <none>
kube-flannel-ds-amd64-l8xtd             1/1     Running   7          9h      10.0.0.2      k8s-master-01   <none>           <none>
kube-flannel-ds-amd64-xcwmt             1/1     Running   0          5h18m   10.0.0.4      k8s-node-02     <none>           <none>
kube-proxy-5gm9j                        1/1     Running   6          9h      10.0.0.2      k8s-master-01   <none>           <none>
kube-proxy-n2gkx                        1/1     Running   0          5h18m   10.0.0.4      k8s-node-02     <none>           <none>
kube-proxy-zl82c                        1/1     Running   2          8h      10.0.0.3      k8s-node-01     <none>           <none>
kube-scheduler-k8s-master-01            1/1     Running   6          9h      10.0.0.2      k8s-master-01   <none>           <none>
```
>全部是Running状态之后，节点的状态就是 Ready 了

至此，Kubernete s集群 1.15 版本部署完成。
<hr>

##### 9. 配置Harbor私有仓库
在 Harbor 主机上更新 Linux 的内核为 4.44 版本和安装 Docker，前面已经叙述过，这里不再赘述。

默认 Docker的仓库是 https 的访问，由于没有证书的原因，所以这里做个假的证书，在局域网内使用的。但是需要告诉Docker这是安全的，可以正常访问的。其它几个节点要做修改：
```python
[root@harbor ~]$ vim /etc/docker/daemon.json 
{
"exec-opts": ["native.cgroupdriver=systemd"],
"log-driver": "json-file",
"log-opts": {
"max-size": "100m"
},
"insecure-registries":["https://hub.atguigu.com"]
}
[root@harbor ~]$ systemctl restart docker
```
将 harbor 安装包从本地主机传到 harbor 服务器：
```python
$ scp harbor-offline-installer-v1.2.0.tgz root@10.0.0.5:/root
```
在线安装 docker-compose，安装到 <kbd>/usr/local/bin</kbd>
```python
[root@harbor harbor]# curl -L "https://github.com/docker/compose/releases/download/1.7.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
[root@harbor harbor]# cd /usr/local/bin/
[root@harbor bin]# chmod a+x docker-compose
```
修改 harbor.cfg 配置文件：
```python
[root@harbor ~]$ tar zxvf harbor-offline-installer-v1.2.0.tgz 
# 将harbor文件夹放到/usr/local/下
[root@harbor ~]$ mv harbor /usr/local/
[root@harbor ~]$ cd /usr/local/harbor/
# 便加harbor配置文件，将原来默认的http和hostname做修改，docker默认是https的
[root@harbor harbor]$ vim harbor.cfg 
ui_url_protocol = https
hostname = hub.atguigu.com
[root@harbor harbor]$ mkdir -p /data/cert/
```
创建 https 证书以及配置相关目录权限证书以及配置相关目录权：
```python
# 跳转到证书目录
[root@harbor cert]$ cd !$
cd /data/cert/
# 申请私钥，输入错误要把文件删除重新输入，两次输入的私钥密码要一样
[root@harbor cert]$ openssl genrsa -des3 -out server.key 2048
# 创建出证书的请求server.csr，需要先输入四要密码
[root@harbor cert]$ openssl req -new -key server.key -out server.csr
Enter pass phrase for server.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:SH
Locality Name (eg, city) [Default City]:SH
Organization Name (eg, company) [Default Company Ltd]:atguigu
Organizational Unit Name (eg, section) []:atguigu
Common Name (eg, your name or your server's hostname) []:hub.atguigu.com
Email Address []:erics1996@yeah.net

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
# 备份私钥
[root@harbor cert]$ cp server.key server.key.org
# 转换成证书。Docker引导采用Nginx当前端的，Docker启动时，如果证书有密码，引导不会成功。所以，需要退下密码。
[root@harbor cert]$ openssl rsa -in server.key.org -out server.key
Enter pass phrase for server.key.org:
writing RSA key
# 退出之后，拿这个证书请求签名。证书生成成功。
[root@harbor cert]$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
Signature ok
subject=/C=CN/ST=SH/L=SH/O=atguigu/OU=atguigu/CN=hub.atguigu.com/emailAddress=erics1996@yeah.net
Getting Private key
# 给证书赋予执行的权限
[root@harbor cert]$ chmod a+x *
```
安装 harbor：
```python
# 执行安装脚本
[root@harbor harbor]$ ./install.sh 
[root@harbor harbor]$ docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS                                                              NAMES
4fd96cb9c1de        vmware/harbor-jobservice:v1.2.0    "/harbor/harbor_jobs…"   7 minutes ago       Up 7 minutes                                                                           harbor-jobservice
9a7993ae3b15        vmware/nginx-photon:1.11.13        "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp, 0.0.0.0:4443->4443/tcp   nginx
a79185e576e2        vmware/harbor-ui:v1.2.0            "/harbor/harbor_ui"      7 minutes ago       Up 7 minutes                                                                           harbor-ui
dc1b31406087        vmware/harbor-adminserver:v1.2.0   "/harbor/harbor_admi…"   7 minutes ago       Up 7 minutes                                                                           harbor-adminserver
1d6178cb1d18        vmware/harbor-db:v1.2.0            "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes        3306/tcp                                                           harbor-db
2fac5e9a43ce        vmware/registry:2.6.2-photon       "/entrypoint.sh serv…"   7 minutes ago       Up 7 minutes        5000/tcp                                                           registry
ede9c0885d71        vmware/harbor-log:v1.2.0           "/bin/sh -c 'crond &…"   7 minutes ago       Up 7 minutes        127.0.0.1:1514->514/tcp                                            harbor-log
```
修改本地域名解析，每一台服务器都需要做解析，也可以相互传送配置好的hosts：
```python
[root@k8s-master-01 ~]$ echo '10.0.0.5 hub.atguigu.com'>>/etc/hosts
[root@k8s-master-01 ~]$ scp /etc/hosts root@10.0.0.5:/etc/
```
安装 harbor 后，可以通过本地浏览器访问 harbor 服务器，默认端口是 80。默认用户名是 <font>admin</font>，密码是：<font>Harbor12345</font>：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611014640549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611015231526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

到这里，我们的仓库就有了。
<hr>

##### 10. Harbor私有仓库的使用
下面看 K8S 可不可以利用上面创建的仓库，当然 K8S 利用之前，Docker 要利用上。使用 Docker 来登录：
```python
[root@k8s-master-01 ~]$ docker login https://hub.atguigu.com
Username: admin
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```
下载一个镜像，然后推送到仓库中：
```python
[root@k8s-master-01 ~]$ docker pull wangyanglinux/myapp:v1
[root@k8s-master-01 ~]$ docker tag wangyanglinux/myapp:v1 hub.atguigu.com/library/myapp:v1
[root@k8s-master-01 ~]$ docker push hub.atguigu.com/library/myapp:v1
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611020641382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

接下来在K8S中启动一些pod，检查能不能利用这个私有仓库
```python
# 查看测试节点master的镜像
[root@k8s-master-01 ~]$ docker images
# 删除上面pull的镜像
[root@k8s-master-01 ~]$ docker rmi -f wangyanglinux/myapp:v1
[root@k8s-master-01 ~]$ docker rmi -f hub.atguigu.com/library/myapp:v1
```
测试 K8S 的集群是否可用以及到底可不可以和 Harbor 镜像仓库连接起来，如果下载次数增加，说明可用：
```python
# 可以先看下pob是怎么运行的，后面会通过资源清单的方式
[root@k8s-master-01 ~]$ kubectl run --help
[root@k8s-master-01 ~]$ kubectl run nginx-deployment --image=hub.atguigu.com/library/myapp:v1 --port=80 --replicas=1
[root@k8s-master-01 ~]$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           17m
# deployment会链接rs
[root@k8s-master-01 ~]$ kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
nginx-deployment-85756b779   1         1         1       17m
[root@k8s-master-01 ~]$ kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-85756b779-pdr56   1/1     Running   0          3m1s
[root@k8s-master-01 ~]$ kubectl get pod -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
nginx-deployment-85756b779-pdr56   1/1     Running   1          13m   10.244.2.4   k8s-node-02   <none>           <none>
```
可以发现pod运行在了 k8s-node-02 节点上，测试 k8s-node-02 节点有没有运行这个 pob，发现有在运行这个pob：
```js
[root@k8s-node-01 ~]$ docker ps -a|grep nginx
49a32eda5558        hub.atguigu.com/library/myapp   "nginx -g 'daemon of…"   3 minutes ago       Up 3 minutes                                      k8s_nginx-deployment_nginx-deployment-85756b779-t9ldc_default_a633801e-4a72-4ff9-baf5-041a1abff8f4_0
d2ca85d6062f        k8s.gcr.io/pause:3.1            "/pause"                 3 minutes ago       Up 3 minutes                                      k8s_POD_nginx-deployment-85756b779-t9ldc_default_a633801e-4a72-4ff9-baf5-041a1abff8f4_0
```
>只要运行了一个pob，就会有/pause

并且可以发现Harber镜像仓库下载数发生了改变，说明 K8S 可以与 Harbor私有仓库进行连接了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061104003787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

如果要访问节点中运行的应用，需要通过私有 IP，<font>这是个扁平化的网络</font>，直接访问也是可以的：
```python
[root@k8s-master-01 ~]$ curl 10.244.1.4
Hello MyApp | Version: v1 | <a href="hostname.html">Pod Name</a>
[root@k8s-master-01 ~]$ curl 10.244.1.4/hostname.html
nginx-deployment-85756b779-t9ldc
[root@k8s-node-01 ~]$ curl 10.244.1.4
Hello MyApp | Version: v1 | <a href="hostname.html">Pod Name</a>
# 用到了pod容器里面的hostname，hostname设置pod名称
[root@k8s-node-01 ~]$ curl 10.244.1.4/hostname.html
nginx-deployment-85756b779-t9ldc
```
使用 Harbor 私有仓库，可以减轻网络资源的压力。
<hr>

##### 11. K8S的优点
如果我们删除一个 pod，发现又会新加入一个	pob，这是因为我们在运行 pod 的时候就已经指明 <font>副本数</font> 一定为1，如果删除了，就不为1了。所以，为了保持副本数为1，会运行一个新的pod：
```python
[root@k8s-master-01 ~]$ kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-85756b779-t9ldc   1/1     Running   1          8h
[root@k8s-master-01 ~]$ kubectl delete pod nginx-deployment-85756b779-t9ldc
pod "nginx-deployment-85756b779-t9ldc" deleted
[root@k8s-master-01 ~]$ kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-85756b779-dzlh7   1/1     Running   0          4s
```
如果一个压力太大，也可以扩容副本。同样副本的期望值已确定，删除 pod 就会运行一个新的pod：
```python
[root@k8s-master-01 ~]$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           8h
[root@k8s-master-01 ~]$ kubectl scale --replicas=4 deployment/nginx-deployment
deployment.extensions/nginx-deployment scaled
[root@k8s-master-01 ~]$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   4/4     4            4           9h
[root@k8s-master-01 ~]$ kubectl get pod
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-85756b779-dzlh7   1/1     Running   0          115s
nginx-deployment-85756b779-jh8gj   1/1     Running   0          13s
nginx-deployment-85756b779-mqfwz   1/1     Running   0          13s
nginx-deployment-85756b779-t48bf   1/1     Running   0          13s
[root@k8s-master-01 ~]$ kubectl get pod -o wide
NAME                               READY   STATUS    RESTARTS   AGE    IP           NODE          NOMINATED NODE   READINESS GATES
nginx-deployment-85756b779-dzlh7   1/1     Running   0          2m1s   10.244.2.7   k8s-node-02   <none>           <none>
nginx-deployment-85756b779-jh8gj   1/1     Running   0          19s    10.244.2.8   k8s-node-02   <none>           <none>
nginx-deployment-85756b779-mqfwz   1/1     Running   0          19s    10.244.1.7   k8s-node-01   <none>           <none>
nginx-deployment-85756b779-t48bf   1/1     Running   0          19s    10.244.1.6   k8s-node-01   <none>           <none>
```
svc 机制其实调度的是 LVS 模块实现负载均衡，调度方式是轮询：
```python
[root@k8s-master-01 ~]$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   27h
[root@k8s-master-01 ~]$ kubectl expose deployment nginx-deployment --port=30000 --target-port=80
service/nginx-deployment exposed
[root@k8s-master-01 ~]$ kubectl get svc
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)     AGE
kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP     27h
nginx-deployment   ClusterIP   10.109.155.157   <none>        30000/TCP   3s
[root@k8s-master-01 ~]$ ipvsadm -Ln
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port Scheduler Flags
  -> RemoteAddress:Port           Forward Weight ActiveConn InActConn
TCP  10.96.0.1:443 rr
  -> 10.0.0.2:6443                Masq    1      3          0         
TCP  10.96.0.10:53 rr
  -> 10.244.0.33:53               Masq    1      0          0         
  -> 10.244.0.34:53               Masq    1      0          0         
TCP  10.96.0.10:9153 rr
  -> 10.244.0.33:9153             Masq    1      0          0         
  -> 10.244.0.34:9153             Masq    1      0          0                
TCP  10.109.155.157:30000 rr
  -> 10.244.1.12:80               Masq    1      0          0         
  -> 10.244.1.13:80               Masq    1      0          0         
  -> 10.244.2.13:80               Masq    1      0          0         
  -> 10.244.2.14:80               Masq    1      0          1         
UDP  10.96.0.10:53 rr
  -> 10.244.0.33:53               Masq    1      0          0         
  -> 10.244.0.34:53               Masq    1      0          0         
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-nns5m
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-jrr2n
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-rb9bm
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-8lsjk
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-nns5m
[root@k8s-master-01 ~]$ curl 10.109.155.157:30000/hostname.html
nginx-deployment-85756b779-jrr2n
[root@k8s-master-01 ~]$ ipvsadm -Ln|grep 10.109.155.157
TCP  10.109.155.157:30000 rr
[root@k8s-master-01 ~]$ kubectl get pod -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP            NODE          NOMINATED NODE   READINESS GATES
nginx-deployment-85756b779-8lsjk   1/1     Running   0          27m   10.244.2.14   k8s-node-02   <none>           <none>
nginx-deployment-85756b779-jrr2n   1/1     Running   0          27m   10.244.1.13   k8s-node-01   <none>           <none>
nginx-deployment-85756b779-nns5m   1/1     Running   0          28m   10.244.2.13   k8s-node-02   <none>           <none>
nginx-deployment-85756b779-rb9bm   1/1     Running   0          27m   10.244.1.12   k8s-node-01   <none>           <none>
```
在外部访问 Kubernets 内部的服务，需要修改 svc 的类型改为NodePort：
```js
# 只需要修改svc的类型
[root@k8s-master-01 ~]# kubectl edit svc nginx-deployment
service/nginx-deployment edited
nginx-deployment   NodePort    10.109.155.157   <none>        30000:30469/TCP   14m
[root@k8s-master-01 ~]# netstat -tunlp|grep 30000
[root@k8s-master-01 ~]# netstat -tunlp|grep 30469
tcp6       0      0 :::30469                :::*                    LISTEN      3043/kube-proxy
```
30469 就是 Kubernets 内部服务暴露到外部的端口，所有节点都可以访问内部服务：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611134301291.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/202006111344533.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200611134420289.png)
