![Zabbix](../imgs/zabbix/202011061125.png)
#### Zabbix遇到的问题
<hr>

##### 1. Please create database manually, and set the configuration parameters for connection to this database. Press "Next step" button when done.
问题来源：连接 mariadb 的时候总是连接不上
解决方法：创建 zabbix 数据库的时候应该这样创建：<font>create database zabbix character set utf8 collate utf8_bin;</font>
<hr>

##### 2. Get value from agent failed: cannot connect to [[10.0.0.3]:10050]: [113] No route to host
问题来源：zabbix server 连接监控主机时提示以上
解决方法：关闭监控主机的防火墙，<font>systemctl stop firewalld</font>，永久关闭使用 <font>systemctl disable firewalld</font>。
<hr>

##### 3. cannot start alert manager service: Cannot bind socket to "/tmp/zabbix_server_alerter.sock": [13] Permission denied.
问题来源：Zabbix 从低版本升级到高版本后重启 Zabbix Server 失败，日志报这个错误
解决方法：关闭 SELINUX 防火墙就可了，将SELINUX的值设置为 disable，具体操作如下：
```python
[root@localhost ~]$ vim /etc/selinux/config
[root@localhost ~]$ grep -Ev '^$|#' /etc/selinux/config 
SELINUX=disable
SELINUXTYPE=targeted
```
><font>配置完成后要重启系统!!!</font>

<hr>

##### 4. cannot start preprocessing service: Cannot bind socket to "/tmp/zabbix_server_preprocessing.sock": [13] Permission denied
问题来源：同上
解决方法：同上
<hr>

##### 5.active check configuration update from [x.x.x.x:10051] started to fail (ZBX_TCP_READ() timed out)
问题来源：Zabbix Proxy下的客户端（Zabbix Agent）日志报错
解决方法：实际上这是不影响监控系统的使用，只需要在配置文件 <font>/etc/zabbix/zabbix_agentd.conf</font> 中把 <font>ServerActive=xxxx</font> 给注释了，不用配置。
<hr>

##### 6. failed to accept an incoming connection: connection from "x.x.x.x" rejected, allowed hosts: "x.x.x.x"
问题来源：使用 Zabbix Proxy 作代理监控 Zabbix Agent 时，客户端（Zabbix Agent）报错
解决方法：在Web页面上添加代理下的监控主机时，这些监控主机中Zabbix Agent的配置文件中 <font>Hostname的值一定要于这里的“主机名称”的值完全相同</font>。
```python
[root@proxy-agent ~]$ grep -Ev "^$|#" /etc/zabbix/zabbix_agentd.conf 
...
Hostname=Zabbix agent
...
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200604030805563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)