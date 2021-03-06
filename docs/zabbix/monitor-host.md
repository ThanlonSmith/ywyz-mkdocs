![Zabbix](../imgs/zabbix/202011061125.png)
#### Zabbix添加监控主机
<hr>

##### 1. 在zabbix-server上安装监控主机
```python
# 安装agent
[root@Zabbix-server ~]$ yum install zabbix-agent.x86_64 -y
# 设置开机启动
[root@Zabbix-server ~]$ systemctl enable zabbix-agent
# 启动agent
[root@Zabbix-server ~]$ systemctl start zabbix-agent
# 查看默认启动的端口
[root@Zabbix-server ~]$ netstat -tunlp|grep 1005
tcp        0      0 0.0.0.0:10050           0.0.0.0:*               LISTEN      9903/zabbix_agentd  
tcp6       0      0 :::10050                :::*                    LISTEN      9903/zabbix_agentd 
```
<hr>

##### 2. 在其它服务器上安装监控主机
```python
# 下载rpm包
[root@agent ~]$ axel https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/7/x86_64/zabbix-agent-4.0.20-1.el7.x86_64.rpm

# 安装agent
[root@agent ~]$ rpm -ivh zabbix-agent-4.0.20-1.el7.x86_64.rpm

# 修改agent配置文件，将Server=127.0.0.1修改为10.0.0.2
[root@agent ~]$ vim /etc/zabbix/zabbix_agentd.conf

# 启动zabbix-agent
[root@agent ~]# systemctl enable zabbix-agent
[root@agent ~]# systemctl start zabbix-agent
```
<hr>

##### 3. 添加监控主机
通过在 Web 界面上配置来添加监控主机：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200520175335473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

关联模板，没有模板就没有监控项，可以先使用默认的模板，监控常规项：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200520175513747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052017574618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)