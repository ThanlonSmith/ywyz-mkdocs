![Zabbix](../imgs/zabbix/202011061125.png)
#### Zabbix分布式监控
<hr>

##### 1. Zabbix分布式监控系统概述
Zabbix有三种监控架构，分别是 Server-Agent、Server-Node-Agent 和 Server-Proxy-Agent。在大型环境中 Zabbix 有两种解决方案，<font>使用节点 ( Node ) 和使用代理 ( Proxy )</font>。使用节点和代理是有区别的：

( 1 ) Proxy 用于 <font>本区域数据收集并将数据发送给 Zabbix Server </font>，它不做分析监控以及数据展示。Proxy 只有一个 proxy 的 daemon 进程，Proxy 也有自己的数据库（不完整的），但是它的数据库只会保存一定时间的数据，它与Master通信是将一批信息打包后发送到 Master。Master 将这些数据 merger 到 Master 数据库。

( 2 ) Node 提供完整的 Zabbix Server 用于 <font>建立分布式监控的层级</font>。Node本身是一台 Zabbix Server，它有 <font>完整的 Web 页面和完整的数据库，它会将数据源源不断地传给 Master</font>。

<font>节点是重量级应用，而代理是轻量级的应用</font>。Master-Proxy 相比 Master-Node 的优点有：Proxy 压力小，数据库只存储一定时间的数据；Master 压力变小，数据不是源源不断获取，减小IO压力（<font>Node 是收集数据就发数据，Proxy 是保存一定时间然后一次性发送</font>）；<font>架构更清晰，容易维护</font>（在 Master 的 Zabbix Server 集中统一配置）。
<hr>

##### 2. Server-Node-Agent架构特性
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603104016375.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

Server-Node-Agent 架构是用来 <font>解决主机过多时单台Server面临性能瓶颈</font> 的问题。方案有使用多个实例，没有实例是独立的一套 Zabbix，有数据库和前台（可选）。该架构支持热插拔，Node 和 Server 的连接可以随时断开，但不会影响 Node 的正常运行。Node 定时向 Server 发送 Configuration，History，Event 等。Server 定时给 Node 发送 Configuration。<font>所有的配置变更只能在 Node 节点操作</font>，不能在 Server 操作。支持树状结构，Node 又可以是个 Server。
<hr>

##### 3. Server-Proxy-Agent架构特性	
![在这里插入图片描述](https://img-blog.csdnimg.cn/202006031044252.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200603184123329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

Proxy 不会向 Server 同步 Configuration，只会接收。Proxy 的数据库会 <font>定时将数据传送给 Server，本地数据库只保存最近没有发送的数据。</font> 其实，Proxy 仅仅是 <font>数据收集器</font>，它不具有触发器、生成事件或发送警报等功能。<font>Zabbix Server 的功能有：监控远程区域；监控不可靠通信的位置；减轻 Zabbix Server 的压力；简化分布式监控的维护。</font>与 Zabbix Server 是单 TCP 连接的，这种方式很容易绕过防火墙，只需要配置一条防火墙规则。

><font>注意：Zabbix Proxy必须使用一个单独的数据库，不可以于Server使用同一个数据库。也不建议放到同一个物理服务器上</font>。

Zabbix的特性：

| 特性项 | 是否支持 |
|--|--|
| **Zabbix agent checks** | **Yes** |
| **Zabbix agent checks(active)** |**Yes**  |
| **Simple checks** | **Yes** |
| **Trapper items** | **Yes** |
| **SNMP checks**| **Yes** |
|  **SNMP traps** | **Yes** |
|  **IPMI checks** | **Yes** |
|  **JMX checks** | **Yes** |
|  **Log file monitoring** | **Yes** |
|  **`Internal checks`** | **`No`** |
|  **SSH checks** |**Yes**  |
|  **Telnet checks** | **Yes** |   |  
| **External checks** | **Yes** |
|  **Buit-in web monitering** | **Yes** |
|  **Network discovery** | **Yes** |
|  **Low-level discovery** |**Yes**  |
|  **`Calculating triggers`** | **`No`** |
|  **`Processing events`** |**`No`**  |
|  **`Sending alerts`** | **`No`** |
|  **`Remote commands`** |  **`No`**|

<hr>

##### 4. 分布式监控的实现
准备3台服务器，一台作 Zabbix Server，另外一台作 Zabbix Proxy，剩下一台作 Zabbix Proxy 的 Agent。这三台服务器分别对应 10.0.0.2、10.0.0.5 和 10.0.0.6。Zabbix Server和Zabbix Agent已经在前面介绍过，这里不赘述。为了测试方便，这里把MySQL和Zabbix Proxy放在统一台服务器上，首先安装与配置数据库：
```python
# 安装MySQL数据库
[root@zabbix-proxy ~]$ yum install mariadb-server
# 初始化数据库
[root@zabbix-proxy ~]$ mysql_secure_installation 
# 设置开机自启
[root@zabbix-proxy ~]$ chkconfig mariadb on
# 创建zabbix_proxy数据库
MariaDB [(none)]> create database zabbix_proxy character set utf8 collate utf8_bin;
# 创建新用户并且授予权限
MariaDB [(none)]> grant all on zabbix_proxy.* to zbxuser@'%' identified by '123456';
# 刷新权限
MariaDB [(none)]> flush privileges;
```
安装完成之后开始安装 Zabbix Proxy：
```python
# 下载包含数据库插件的 Zabbix Proxy包
[root@zabbix-proxy ~]$ wget https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-proxy-mysql-4.0.21-2.el7.x86_64.rpm
# 下载epel源
[root@zabbix-proxy ~]$ wget https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-7-12.noarch.rpm
# 安装epel源(也可以使用rpm)
[root@zabbix-proxy ~]$ yum install epel-release-7-12.noarch.rpm
# 安装Zabbix Proxy。使用yum安装会自动解决依赖问题，自动安装依赖的fping等
[root@zabbix-proxy ~]$ yum localinstall -y zabbix-proxy-mysql-4.0.21-2.el7.x86_64.rpm
# 设置Zabbix Proxy开机自启
[root@zabbix-proxy ~]$ chkconfig zabbix-proxy on
# 查看Zabbix Proxy是否启动，以及占用的端口
[root@zabbix-proxy ~]$ netstat -tunlp|grep zabbix
tcp        0      0 0.0.0.0:10051           0.0.0.0:*               LISTEN      1543/zabbix_proxy   
tcp6       0      0 :::10051                :::*                    LISTEN      1543/zabbix_proxy
```
配置 Zabbix Proxy 连接 Zabbix Server 和 MySQL：
```python
# 查询Zabbix Proxy初始数据
[root@zabbix-proxy ~]$ rpm -ql zabbix-proxy-mysql
...
/usr/share/doc/zabbix-proxy-mysql-4.0.21/schema.sql.gz
...
# 解压sql文件
[root@zabbix-proxy zabbix-proxy-mysql-4.0.21]$ gunzip schema.sql.gz 
# 把sql文件导入到数据库
[root@zabbix-proxy zabbix-proxy-mysql-4.0.21]$ mysql -uzbxuser -p zabbix_proxy<schema.sql
# 编辑Zabbix Proxy配置文件
[root@zabbix-proxy ~]$ vim /etc/zabbix/zabbix_proxy.conf 
# 查看需要已经修改过的记录
[root@zabbix-proxy ~]$ grep -Ev '^$|#' /etc/zabbix/zabbix_proxy.conf 
Server=10.0.0.2			# Zabbix Server的地址
Hostname=Zabbix proxy	# 主机名，Web页面上添加代理的时候代理程序名要和这里一致
...
DBHost=localhost		# 数据库的地址(我这里在本地)
DBName=zabbix_proxy		# 数据库名
DBUser=zbxuser			# 数据库用户名
DBPassword=123456		# 数据库的密码
...
# 修改完成后重启Proxy
[root@zabbix-proxy ~]$ systemctl restart zabbix-proxy
# 如果出问题了可以检查日志
[root@zabbix-proxy ~]$ tailf /var/log/zabbix/zabbix_proxy.log 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200604040452422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

配置Zabbix Proxy的Agent：
```python
# 编辑配置文件
[root@proxy-agent ~]$ vim /etc/zabbix/zabbix_agentd.conf 
# 查看需要已经修改过的记录
[root@proxy-agent ~]$ grep -Ev '^$|#' /etc/zabbix/zabbix_agentd.conf 
...
Server=10.0.0.5
ServerActive=10.0.0.5	# 这里不建议配置，否则日志会报：failed to accept an incoming connection: connection from "10.0.0.2" rejected, allowed hosts: "10.0.0.5"，实际上是没有问题的
Hostname=Zabbix agent	# 主机名。Web界面添加监控主机时，主机名称一定与这里完全一致
...
# 如果出问题了可以检查日志
[root@proxy-agent ~]$ tailf /var/log/zabbix/zabbix_agentd.log
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200604040659779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 5. 分布式监控性能调优
提高 Zabbix Server 和 Zabbix Proxy 同步数据的效率，可以减小 Proxy 向 Server 请求同步配置文件的时间，配置参数是 ConfigFrequency，默认 3600s 同步一次配置。Zabbix Proxy 向主服务器打包发一次数据的时间间隔减小，配置参数是 DataSenderFrequency，当然系统默认就是1s：
```python
[root@zabbix-proxy zabbix]$ grep -Ev '^$|#' zabbix_proxy.conf 
...
ConfigFrequency=3600
DataSenderFrequency=1
...
```
ProxyLocalBuffer 是本地数据保存多久，Proxy 收集来自各个Agent的数据要先保存在本地，然后打包发给主服务器，<font>打包发给服务器后还要再保存多久</font>，如果设置了0就表示不保存。如果 <font>Proxy 联系不到主服务器</font>，也就是 Zabbix Server，那么 <font>数据在本地保存的时间</font> 由 ProxyOfflineBuffer 来配置，默认是1，表示1h：
```python
[root@zabbix-proxy zabbix]$ grep -Ev '^$|#' zabbix_proxy.conf 
...
ProxyLocalBuffer=0
ProxyOfflineBuffer=1
...
```