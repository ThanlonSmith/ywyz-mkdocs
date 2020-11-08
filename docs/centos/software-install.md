![](../imgs/centos/202011060247.jpeg)

#### 升级内核版本
<hr>

##### 1. 查看当前内核版本
使用的系统版本，当前日期 CentOS 最新版：
```python
$ cat /etc/redhat-release
CentOS Linux release 8.2.2004 (Core)
```
查看当前系统内核版本：
```python
$ uname -r
4.18.0-193.6.3.el8_2.x86_64
```
当前日期 Linux 的内核很多都 5.x，各方面考虑还是有必要升级一下的，内核可以从这里直接下载：**[https://www.kernel.org/](https://www.kernel.org/)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200708004934128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 2. 使用ELRepo仓库
这里使用 ELRepo 仓库，ELRepo 仓库是基于社区的用于企业级 Linux 仓库，提供对 RedHat Enterprise（RHEL）和其他基于 RHEL 的 Linux 发行版（CentOS、Scientific、Fedora 等）的支持。ELRepo 聚焦于和硬件相关的软件包，包括文件系统驱动、显卡驱动、网络驱动、声卡驱动和摄像头驱动等。网址：**[http://elrepo.org/tiki/tiki-index.php](http://elrepo.org/tiki/tiki-index.php)** :

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070800543126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

导入 ELRepo 仓库的公共密钥：
```python
$ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
```
安装 ELRepo 仓库的 yum 源：
```python
$ yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
```
可用的系统内核安装包：
```python
$ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
bpftool.x86_64                                                                     5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-devel.x86_64                                                             5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-doc.noarch                                                               5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-headers.x86_64                                                           5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-modules-extra.x86_64                                                     5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-tools.x86_64                                                             5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-tools-libs.x86_64                                                        5.7.7-1.el8.elrepo                                                       elrepo-kernel
kernel-ml-tools-libs-devel.x86_64                                                  5.7.7-1.el8.elrepo                                                       elrepo-kernel
perf.x86_64                                                                        5.7.7-1.el8.elrepo                                                       elrepo-kernel
python3-perf.x86_64                                                                5.7.7-1.el8.elrepo                                                       elrepo-kernel
```
<hr>

##### 3. 安装最新版内核
```python
$ yum --enablerepo=elrepo-kernel install kernel-ml
```
<hr>

##### 4. 设置以新的内核启动
0 表示最新安装的内核，设置为 0 表示以新版本内核启动：
```python
$ grub2-set-default 0
```
>以后不需要第5步，直接使用这条指定不同数字设置不同内核版本启动。

<hr>

##### 5. 生成grub配置文件并重启系统
```python
$ grub2-mkconfig -o /boot/grub2/grub.cfg
$ reboot
```
>这一步可以不用执行生成grub配置的命令，直接重启！

<hr>

##### 6. 验证新内核
```python
$ uname -r
5.7.7-1.el8.elrepo.x86_64
```
这个版本就是本文第一张截图中稳定版 v5.7.7
<hr>

##### 7. 查看系统中已安装的内核
可以看到这里一共安装了3个版本的内核，分别是 v4.18.0-193.6.3 和 v4.18.0-147.5.1 以及 v5.7.7-1。
```python
$ rpm -qa | grep kernel
kernel-core-4.18.0-193.6.3.el8_2.x86_64
kernel-modules-4.18.0-147.5.1.el8_1.x86_64
kernel-ml-modules-5.7.7-1.el8.elrepo.x86_64
kernel-devel-4.18.0-147.5.1.el8_1.x86_64
kernel-4.18.0-80.el8.x86_64
kernel-tools-libs-4.18.0-193.6.3.el8_2.x86_64
kernel-core-4.18.0-80.el8.x86_64
kernel-4.18.0-147.5.1.el8_1.x86_64
kernel-modules-4.18.0-80.el8.x86_64
kernel-4.18.0-193.6.3.el8_2.x86_64
kernel-tools-4.18.0-193.6.3.el8_2.x86_64
kernel-ml-5.7.7-1.el8.elrepo.x86_64
kernel-headers-4.18.0-193.6.3.el8_2.x86_64
kernel-core-4.18.0-147.5.1.el8_1.x86_64
kernel-devel-4.18.0-193.6.3.el8_2.x86_64
kernel-modules-4.18.0-193.6.3.el8_2.x86_64
kernel-ml-core-5.7.7-1.el8.elrepo.x86_64
```
<hr>

##### 8. 删除旧内核
删除旧内核，这一步是可选的。
```python
$ yum remove kernel-core-4.18.0  kernel-devel-4.18.0  kernel-tools-libs-4.18.0 kernel-headers-4.18.0
```
再查看系统已安装的内核，确认旧内核版本已经全部删除：
```python
$ rpm -qa | grep kernel
kernel-ml-modules-5.7.7-1.el8.elrepo.x86_64
kernel-ml-5.7.7-1.el8.elrepo.x86_64
kernel-ml-core-5.7.7-1.el8.elrepo.x86_64
```
也可以安装 <kbd>yum-utils</kbd> 工具，当系统安装的内核大于3个时，会自动删除旧的内核版本：
```python
$ yum install yum-utils
```
删除旧的版本使用 <font>package-cleanup</font> 命令。
<hr>

#### 编译安装Python3
<hr>

##### 1. 解决环境依赖
```python
$ yum update -y
$ yum install gcc patch libffi-devel zlib-devel  bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel xz-devel
```
<hr>

##### 2. 下载源代码
```python
$ wget https://www.python.org/ftp/python/3.8.3/Python-3.8.3.tar.xz
```
<hr>

##### 3. 解压缩源代码
```py
$ xz -d Python-3.8.3.tar.xz
$ tar -xf Python-3.8.3.tar
```
<hr>

##### 4. 释放编译文件
```python
$ cd Python-3.8.3/
$ ./configure --prefix=/usr/local/python383
```
>执行完这条语句后还不会生成 /usr/local/python3383 这个文件夹。./configure 是用来检测安装平题啊的目标特征，比如它会检测你是不是有 CC 或 GCC，它是一个 Sheel 脚本。configure 脚本执行后，会生成一个 Makefile 文件。--prefix 参数指明安装路径。

<hr>

##### 5. 编译与安装
```python
$ make && make install
```
>这两步完成后才会创建 /usr/local/python383 文件夹。make 是用来编译的，它从 Makefile 中读取指令，然后编译。make install 是用来安装的，它也会从 Makefile 中读取指令，安装到指定的位置。

<hr>

##### 6. 配置软链接
配置软链接可以更方便地启动 Python。因为系统中不止 v3.8.3 版本的 Python，所以配置的时候最好要区分 Python 版本。<font>相对于第7部分配置环境变量的方式，最好选择配置软链接的方式，不然会很乱。</font>
```python
$ ln -s /usr/local/python383/bin/python3.8 /usr/bin/python3.8
$ python3.8 -V
$ ln -s /usr/local/python383/bin/pip3.8 /usr/bin/pip3.8
$ pip3.8 -V
```
<hr>

##### 7. 配置环境变量
配置环境变量的方式就是把 Python3.8 目录加入到系统环境变量中，如果系统中不打算安装不同的版本的 Python 共存，使用配置环境变量的方式是非常好的选择。获取系统的环境变量 PATH： 
```python
$ echo $PATH
```
将加入 Python3.8 目录而更改后的环境变量 PATH 写入到个人配置文件中，<font>用户登录系统时候生效</font>：
```python
$ vim /etc/profile
```
将 **`PATH=$PATH:/usr/local/python383/bin/`** 放到 profile 文件的最后。读取配置文件，生效配置：
```python
$ source /etc/profile
```
<hr>

#### 安装MySQL
<hr>

##### 1. 卸载旧版本
查询服务器中是否有以rpm包方式安装的mysql，如果有使用 **`rpm -e`** 将其删除：
```python
$ rpm -qa | grep -i mysql
$ whereis mysql
$ find / -name mysql
```
<hr>

##### 2. 下载安装包
官网：**[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)**，我这里选择 RHEL8对应的版本：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200709130527136.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

下载安装包：
```python
$ wget https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.20-1.el8.x86_64.rpm-bundle.tar
```
解压安装包：
```python
$ tar xvf mysql-8.0.20-1.el8.x86_64.rpm-bundle.tar
```
<hr>

##### 3. 安装
不需要安装所有的包，只需要按照顺序安装以下包：
```python
$ rpm -ivh mysql-community-common-8.0.20-1.el8.x86_64.rpm
$ rpm -ivh mysql-community-libs-8.0.20-1.el8.x86_64.rpm
$ rpm -ivh mysql-community-client-8.0.20-1.el8.x86_64.rpm
$ rpm -ivh mysql-community-server-8.0.20-1.el8.x86_64.rpm
```
<hr>

##### 4. 初始化数据库
mysql 8.0 不再有 my.ini 配置文件，我们通过 **`mysqld --initialize --console`** 命令可以自动生成MySQL的初始化配置（ data文件目录等 ），
```python
$ mysqld --initialize --console
```
>执行 mysql_secure_installation 命令做一些常规化安全设置。

<hr>

##### 5. 启动服务
```python
$ systemctl start mysqld
```
<hr>

##### 6. 设置密码
此时数据库没有密码，不使用密码也是不能登录的，所以需要设置密码。编辑配置文件，在最后面加上 **`skip-grant-tables`** 参数：
```python
$ vim /etc/my.cnf
```
保存退出后要重启 MySQL 服务，这个时候就可以免密登录：
```python
$ mysql -uroot -p
$ mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
```
将 root 密码设置为空：
```sql
mysql> use mysql
mysql> update user set authentication_string='' where user='root';
mysql> exit
```
编辑配置文件把 <kbd>skip-grant-tables</kbd> 参数注释掉或者删除掉：
```python
$ vim /etc/my.cnf
$ systemctl restart mysqld
```
重新登录：
```python
$ mysql
$ alter user 'root'@'localhost' identified by 'Root123!';
```
>要注意密码不能设置得太简单，否则不能设置成功!

<hr>

##### 7. 卸载
查询软件包：
```python
$ rpm -qa |grep -i mysql
mysql-community-libs-8.0.20-1.el8.x86_64
mysql-community-common-8.0.20-1.el8.x86_64
mysql-community-client-8.0.20-1.el8.x86_64
mysql-community-server-8.0.20-1.el8.x86_64
```
卸载：
```python
yum remove mysql-community-libs-8.0.20-1.el8.x86_64
yum remove mysql-community-common-8.0.20-1.el8.x86_64
```
>如果存在依赖，被依赖的包被删除，依赖的包也会被删除!

查找mysql相关目录/文件删除：
```python
$ find / -name mysql
```
删除配置项和日志：
```python
rm -rf /etc/my.cnf.backup 
rm -rf /etc/my.cnf.rpmsave 
rm -rf /var/log/mysqld.log
```
>如果不删除 mysqld.log 这个文件，会导致再次安装的mysql无法生成新密码，导致无法登录!

<hr>

#### 配置静态IP
<hr>

##### 1. 设置虚拟网络
![](https://img-blog.csdnimg.cn/20200406002344878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200406002350279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406002355779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

<hr>

##### 2. 查看服务器使用的网卡
```js
[root@haproxy-server ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:99:c6:e4 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.2/24 brd 10.0.0.255 scope global noprefixroute ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::6970:bf11:3602:3813/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```
<hr>

##### 3. 修改网卡对应的配置文件
```js
[root@haproxy-server ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens33
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=02e038be-6008-4c4c-af40-c29166a13239
DEVICE=ens33
```
修改 BOOTPROTO、ONBOOT 的值，添加 IPADDR、NETMASK、GATEWAY 和 DNS1：
```js
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static          
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=02e038be-6008-4c4c-af40-c29166a13239
DEVICE=ens33
ONBOOT=yes
IPADDR=10.0.0.2
NETMASK=255.255.255.0
GATEWAY=10.0.0.254
DNS1=119.29.29.29
```
<hr>

##### 4. 重启网卡测试网络情况
```js
[root@haproxy-server ~]# service network restart
Restarting network (via systemctl):                        [  OK  ]
[root@haproxy-server ~]# ping www.huawei.com
PING www.huawei.com.lxdns.com (101.227.102.169) 56(84) bytes of data.
64 bytes from 101.227.102.169 (101.227.102.169): icmp_seq=1 ttl=128 time=6.04 ms
64 bytes from 101.227.102.169 (101.227.102.169): icmp_seq=2 ttl=128 time=6.30 ms
64 bytes from 101.227.102.169 (101.227.102.169): icmp_seq=3 ttl=128 time=7.17 ms
64 bytes from 101.227.102.169 (101.227.102.169): icmp_seq=4 ttl=128 time=7.45 ms
^C
--- www.huawei.com.lxdns.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 12027ms
rtt min/avg/max/mdev = 6.041/6.746/7.459/0.593 ms
```
