![](../imgs/linux/linux-basis/20201105.jpeg)
#### Linux进阶
<hr>

##### 1. vim
vim 是从 vi 发展出来的一个文本编辑器。其代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。vi 是老式的字处理器，不过功能已经很齐全了，但是还是有可以进步的地方，vim 则做到了。vim 可以说是程序开发者的一项很好用的工具。

基本上 vi/vim 共分为三种模式，分别是命令模式(Command mode)，输入模式(Insert mode)和底线命令模式(Last line mode)。vim 具有程序编辑功能，可以主动以字体颜色辨别语法的正确性，方便程序设计。

( 1 ) vi/vim的三种工作模式

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190930183152316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

( 2 ) 命令模式
```js
w或者e：移动光标到下一个单词
b：移动光标到上一个单词
数字0：移动到本行的开头
$：移动到光标到本行结尾
H：移动光标到屏幕首行
M：移动光标到屏幕的中间行
L：移动光标到屏幕的尾行
gg：移动光标到文档的首行
G：移动光标到文档的尾行
ctrl+f：上一页
ctrl+b：下一页
/root：在整篇文章中搜索root字符串，向下查找，按n查找下一个
?root：在整篇文章中搜索root字符串，向上查找，安n查找下一个
%：找到括号的另一半
yy：拷贝光标所在行
dd：删除光标的所在行
D：删除当前光标到行尾的内容
dG：删除当前行到文档尾部的内容
p：粘贴yy操作所复制的内容
x：删除光标所在的字符
u：撤销上一步操作
3yy：拷贝光标所在的3行
5dd：删除光标所在的5行
```
( 3 ) 输入模式
在命令模式下按 i o a 即可进入输入模式。

( 4 ) 底线命令模式
```
:q：退出
:q!：强制退出
:w：生效写入的内容
:wq!：强制写入退出
:set num或set number：显示行号
:set nonu或set nonumbeer：取消行号
:数字：调到数字那行
:!command：暂时离开vim指令模式，执行command的结果，如:!ip a表示临时看一下ip信息，然后回到vim
```
随时按下ESC可以退出底线命令模式，进入命令模式。
<hr>

##### 2. 网络相关的命令
如果你的电脑上没有 `ifconfig`，只有 `ip addr`，则需要通过 `yum -y install net-tools` 来安装。

查看网卡的 ip 地址：`$ ifconfig`，直接输入 ifconfig 会列出已经启动的网卡，也可以输入 ifconfig 单独显示 eth0 的信息。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191001062529451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
```
who1：网卡的代号
lo：回环地址loopback
inet：IPv4的ip地址
netmask：子网掩码
broadcast：广播地址
RX/TX：流量发/收情况，RX是发送(transport)，TX(receive)
packets：数据包数
errors：数据包错误数
dropped：数据包有问题被丢弃的数量
collisions：数据包碰撞情况，数值大表示网络状况差
```
启动/关闭一块网卡：`$ ifup eth0`表示启动一块网卡，`$ ifdown eth0` 表示关闭一块网卡。此命令其实是调用网络哦服务配置文件 `/etc/init.d/network`。`$ /etc/init.d/network start` 是启动网卡，`$ /etc/init.d/network stop` 是停止网卡，`$ /etc/init.d/network restart` 是重启网卡。ifup 和 ifdown 是直接连接到 /etc/sysconfig/network-scripts 目录下搜索对应的网卡文件，例如 ifcfg-eth0 然后加以设置。

ip 命令：ip 结合了 ifconfig 和 route 两个命令的功能，`ip addr show` 表示查看ip信息。

netstat 命令用来打印 Linux 中网络系统的状态信息，可得知 Linux 系统的网络状态。一般使用 `netstat -tunlpa `，netstat 指令可以查看到系统开启的端口。可以过滤指定端口，如指定3306端口：`netstat -tunlpa | grep 3306`
```
t或--tcp：显示TCP传输协议的连接状态

u或--udp：显示UDP传输协议的连接状态

n或--numeric：直接使用ip地址，而不通过域名服务器

l或--listening：显示监控中的服务器Socket

p或--programs：显示正在使用Socket的程序识别码和程序名称

a或--all：显示所有连线中的Socket
```
测试网速的命令：`speedtest-cli`，centos 中需要安装 speedtest-cli，可以执行 `pip install git+https://github.com/sivel/speedtest-cli.git`，使用 git 从 git 仓库中下载 speedtest-cli，要保证系统已经安装 git 和 python 的 pip 模块。
```py
thanlon@thanlon-master:~$ speedtest-cli 
Retrieving speedtest.net configuration...
Testing from China Telecom Shanghai (101.92.63.73)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Chinamobile-5G (Shanghai) [19.64 km]: 12.642 ms
Testing download speed................................................................................
Download: 4.60 Mbit/s
Testing upload speed......................................................................................................
Upload: 2.01 Mbit/s
```
<hr>

##### 3. 查看系统信息的命令
查看系统版本信息：`$ cat /etc/redhat-release`

查看内核版本号：`$ uname -r`

查看系统位数：`$ uname -m`

查看内核所有信息：`$ uname -a`

查看主机名：`$ hostname`

修改主机名：`$ hostnamectl set-hostname master`，设置主机名为：thanlon-linux

查看系统处理器个数：`cat /proc/cpuinfo`，或者使用`$ top`,然后按1
<hr>

##### 4. 用户管理
`/etc/passwd`存放用户组信息，`/etc/shadow`存放用户密码，`/etc/group`存放用户组。注意：超级用户 root 切换到普通用户不需要密码；普通用户切换到 root 需要输入密码；普通用户较小，只能查看信息；$ 符号是普通用户的命令提示符，# 是超级用户的命令提示符。

通过 id 命令查看用户信息：`$ id root`，`$ id thanlon`

普通用户的创建：`$ useradd kiku` 表示创建用户kiku，可以通过 `tail -1 /etc/passwd` 查看kiku的信息

普通用户的删除：`$ userdel -rf thanlon`，删除thanlon这个普通用户。-f 表示强制删除用户，-r 表示删除用户以及用户家目录。

更改用户密码：`$ passwd root` 根改root用户密码，`$ passwd thanlon` 更改 thanlon 用户的密码。thanlon是普通用户，是管理员或具备管理权限的用户创建的，只能读，不能增删改。

切换用户：`$ su - root` 切换到root用户，`$ su - thanlon` 切换到thanlon用户。<font>su 命令中间的 - 号很重要</font>，意味着完全切换到新的用户，即环境变量信息也变更为新用户的信息。

查看当前用户：`$ whoami`

退出用户登录：`$ logout`

创建用户组：`$ groupadd it_dep` 表示添加用户组 it_dep。groupadd 用于创建用户组，为了高效地指派系统中各个用户的权限，在工作中常常添加几个用户到一个组里面，这样可以针对一类用户安排权限。

sudo：sudo 命令用来以其它身份来执行命令，预设的身份为 root。在 /etc/sudoers 中设置了可执行 sudo 指令的用户。若其未经过的用户企图使用 sudo，则会发出警告的邮件给管理员。用户使用 sudo 时，必须先输入密码，之后有5分钟的有效期限，超过期限则必须重新输入秘密。<kbd>sudo【选项】【参数】</kbd>
```
-b：在后台执行指令
-h：显示帮助
-H：将HOME环境变量设置为新身份的HEMO环境变量
-K：结束密码的有效期限，也就是下次执行sudo时便需要输入密码
-l：列出目前用户可执行与无法执行的指令
-p：改变询问密码的提示符
-s<shell>：执行指定的shell
-u<用户>：以指定的用户作为新的身份，若不加此参数，则预设以root作为新的身份
-v：延长密码有效期5分钟
-V：显示版本信息
```
sudo 命令应用在什么时候应用？当用户权限小而想要执行更多权限的时候，可以以 root 身份去运行。这里由于配置sudo必须编辑 /etc/sudoers 文件，并且只有 root 才能修改。我们可以通过 `$ visudo` 命令直接编辑 sudoers 文件，使用这个命令还可以检查语法，比直接使用vim编辑更加安全。找到 root ALL=(ALL:ALL) ALL，可以在其下面为普通用户thanlon添加 <font>在任何地方执行任何命令</font>。
```py
# User privilege specification
root    ALL=(ALL:ALL) ALL
thalon ALL=(ALL:ALL) ALL # 允许thanlon在任何地方执行任何命令
```
##### 5. 文件与目录权限
在 Linux 中，每个文件都有所有者和所属组，并且规定了文件的所有者，所属组以及其他人对文件的可读、可写、可执行权限。对于目录的权限来说，可读是读取目录的列表，可写是在目录内新增、修改和删除文件，可执行表示可以进入目录。

( 1 ) 查看文件的权限
```shell
$ ls -l tmp.txt 
-rwxrwxrwx 1 thanlon thanlon 30 9月  19 19:38 tmp.txt
```
( 2 ) 文件类型
```shell
 - ：一般文件
 d：文件夹
 l：软链接(快捷方式)
 b：块设备，存储媒体文件为主
 c：代表键盘、鼠标等设备
```
( 3 ) 文件权限
```python
r：read是可读，可以用cat等命令查看
w：write是可写，可以编辑或者删除这个文件
x：executable可执行
```
( 4 ) 目录权限
```python
r：可以对此目录执行ls列出所有文件
w：可以在这个目录创建文件
x：可以cd进入这个目录或者查看详细信息
```
( 5 ) 数字权限
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191001141950133.png)
( 6 ) 查看文件权限相关的命令
查看 root 用户的权限：`$ id root`

( 7 ) 修改文件权限
修改用户数主：`$ chown thanlon xxx.txt`，修改xxx.txt文件的所属主为thanlon。

修改用户属组：`$ chgrp 组名 file`

给属组加上可读可执行权限：`$ chmod g+w test.txt`$ chmod g+x test.txt`

给属主加上可读可执行权限：`$ chmod u+w test.txt`$ chmod u+x test.txt`

减去其他用户的可读权限：`$ chmod o-r test.txt`

给文件添加可读可写可执行的权限：`$ chmod 777 test.txt`

( 8 ) 软链接

软链接也叫做符号链接，类似于 windows 的快捷方式，常用于安装软件的快捷方式配置 python、nginx 等。

软链接格式：`$ ln -s 目标文件 软链接名`，可以通过软链接查看文件 `$ cat 软链接名`。可以通过 `$ rm -rf 软链接地址`来删除快捷方式，如果删除了目标文件，那么软链接也就没了意义。
<hr>

##### 6. PS1变量
PS1 用于控制命令提示符，可修改：

打印 PS1：`$ echo $PS1`，打印结果是[\u@\h \W]\$
```py
PS1='[\u@\h \W \t]\$' # 可以添加一个时间
PS1='[\u@\h \w]\$' # 可以展示绝对路径
```
这个操作后，重启后变量配置会丢失，所以写入到系统的配置文件中，每次登录都加载。将 “PS1='[\u@\h \W \t]\$'” 写入到 /etc/profile 这个用户配置文件最下面一行中。`systemctl stop firewalld`这条命令可以停止防火墙。
<hr>

##### 7. tar解压命令
tar 命令是用来压缩和解压文件，tar 本身不具有压缩功能，它是调用压缩功能实现的。

常用的参数：
```
-c或--create：建立新的备份文件，压缩文件使用这个参数
-x或--extract或--get：从备份文件中还原文件，解压缩使用这个参数
-z或--gzip或--ungzip：通过gzip指令处理备份文件
-f<备份文件>或--file=<备份文件>：指定备份文件
-v：显示操作过程
-j：支持bzip2解压文件
```
压缩当前目录所有jpg结尾的文件：`tar -zcvf all_pic.tgz *.jpg`

压缩文件：`tar -zcvf xxx.tar.gz xxx.txt`等价于`tar -cvf xxx.tar xxx.txt`和`gzip xxx.tar`

解压文件：`tar -xvf all_pic.tgz`，一般使用的解压：`tar -zxvf tmp.tgz`

解压到指定目录：`tar zxvf Python-3.8.0.targz -C /home/thanlon/`

解压 xz 结尾的文件：`xz -d Python-3.8.0.tar.xz` 然后使用`tar -xf Python-3.8.0.tar`

解压缩 bz2 结尾的文件：`tar -xjf xx.tar.bz2`
<hr>

##### 8. gzip解压命令
gzip 用来压缩文件，是一个使用广泛的压缩程序，被压缩的以 ".gz" 扩展名。gzip可以压缩较大的文件，以 60%～70% 压缩率来节省空间，<font>更多的使用的是 tar</font> :
```
-d或--decompress或----uncompress：加开压缩文件
-f或--force：强行压缩文件
-h或help：在线帮助
-l或--list：列出压缩文件的相关信息
-L或--license：显示版本与权限信息
-r或--recursive：递归处理，将指定目录下的所有文件以及子文件目录一并处理
-v或--verbose：显示指令执行过程
```
压缩当前目录每一个文件为.gz文件：`gzip *`

解压缩文件并列出详细的信息：`gzip -dv *`

显示压缩文件的信息，并不解压：`gzip -l *`

压缩一个tar备份文件，扩展名是tar.gz：`tar -cf my.tar my.py`，`gzip -r my.tar`
<hr>

##### 9. 查看进程的状态
查看 8000 端口有没有正常启动，使用 `netstat -tunlp|grep 8000`，那么查看进程有没有运行，可以使用 ps 相关命令。格式：**`ps 参数`**
```
-a：显示所有进程
-u：用户以及其他详细信息
-x：显示没有控制终端的进程
```
查看那更多可以使用man命令：`man ps`

查看 Python 进程可以使用：`ps -ef | grep python`，查看redis、mysql、nginx进程可以使用相同的命令：`ps -ef | grep redis`、`ps -ef | grep mysql`、`ps -ef | grep nginx`。
<hr>

##### 10. kill命令
kill 命令用来删除执行中的程序，kill 可以将指定的信息送至程序。

kill 命令相关的参数：
```
-a：当处理当前进程时，不限制命令和进程号的对应关系
-l <信号编号>：若不加<信号编号>选项，则-l参数会列出全部的信息名称
-p：指kill命令只打印相关进程的进程号，而不发送任何信号
-s <信号名称或编号>：指定要送出的信息
-u：指定用户
```
只有 9 中信号可以无条件终止进程，其它信号进程都有权力忽略，下面是常用的信号：
```
HUP    1：终端断线
INT    2：中断(同Ctrl + C)
QUIT   3：退出(同Ctrl + \)
TERM   15：终止
KILL   9：强制终止
CONT   18：继续(与STOP相反，fg/bg命令)
STOP   19：暂停(同Ctrl + Z)
```
kill 进程前可以通过 `ps -ef` 查看进程 id 等信息：
```
用户名   pid                                           进程执行命令
root    1041     1  0 21:44 ?        00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
```
比如上面的进程 id 是 1041，可以执行 `kill 1041`，如果遇到杀不死的进程，可以执行 `kill -9 1041`，-9 是发给 kill 命令的一个信号，强制杀死信号。
<hr>

##### 11. killall命令
通常来讲，复杂软件的服务程序会有多个进程协同为用户提供服务，如果逐个去结束这些进程会比较麻烦，此时可以使用 killall 命令来批量结束某个服务下横徐带来的全部进程。简单来讲，killall 命令一次性可以杀死多个有依赖的进程。
 
例如，nginx 启动后有两个进程，可以直接执行`killall nginx`
<hr>

##### 12. selinux防火墙
防火墙可以防止服务器被恶意攻击，保护系统资源。比如，恶意攻击你的端口进程等。简而言之，防火墙就是定义一些规则，控制那些ip可以访问哪些 ip 不可以访问。定义一些当前服务器允许那些端口可以被访问呢，哪些端口不可以被访问。selinux 是 Linux 内置防火墙、iptables 和 firewalld 是 Linux 的软件防火墙，可能还会有一些硬件防火墙。

大多数ssh连接不上服务器，都是因为服务器防火墙阻挡了，这里介绍 Linux 内置的防火墙 selinux。其配置文件在 /etc/selinux/config

查看selinux状态：`getenforce`

永久关闭selinux：通过修改配置文件，然后重启机器可以永久关闭selinux防火墙：`sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config`，在使用sed命令之前，可以通过cat 和 grep 命令查看配置文件中的配置状态：`grep -i selinux /etc/selinux/config`

临时关闭selinux(不需要重启机器，重启后失效)，首先获取selinux状态`getenforce`，然后临时关闭`setenforce`
<hr>

##### 13. iptables防火墙

查看 iptables 规则：`iptables -L`

清空 iptables 规则：`iptables -F`

关闭 iptables 服务：`systemctl stop firwalld`

centos7 默认使用 firwalld 作为防火墙，关闭 firwalld 的过程如下：
```py
systemctl status firewalld # 查看防火墙状体
systemctl stop firewalld # 关闭防火墙
systemctl disable firewalld # 关闭防火墙开机启动
systemctl is-enabled firewalld.service # 检查防火墙是否启动
```
<hr>

##### 14. Linux中文显示设置
设置 Linux 中文显示，可以防止 Linux 中中文乱码。Linux 下常用的字符集有 GBK 和 UTF-8，GBK实际上企业使用得比较少，广泛使用 UTF-8。
```python
# 查看系统当前字符集
echo $LANG
# 修改字符集
export LANG=en_US.utf8
# 或者这样修改字符集
1.修改配置文件vim /etc/locale.conf
LANG="zh_CN.UTF-8"
2.使生效
source /etc/locale.conf
3.更改后查看系统语言变量
locale
```
如果出现乱码，则：
```python
1.系统字符集utf8
2.xshell字符集utf8
3.文件字符集一致zh_CN.UTF-8
```
<hr>

##### 15. df命令
df命令用于显示磁盘分区上可使用的磁盘空间，默认显示单位为KB。可以使用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

常见的参数:
```
-h或--human-readable：以可读向较高的方式来显示信息
-k或--kilobytes：指定区块大小为1024字节
-T或--print-type：显示文件系统的类型
--help：显示帮助
--version：显示版本信息
```
查看系统磁盘设备，默认是KB单位：`$ df`

使用 -h 参数以KB以上的单位来显示可读性高：`$ df -h`，查看磁盘使用容量
<hr>

##### 16. tree命令
以树状图显示文档目录结构，tree 命令的参数：
```
-a：显示所有文件和目录；
-A：使用ASNI绘图字符显示树状图而非以ASCII字符组合；
-C：在文件和目录清单加上色彩，便于区分各种类型；
-d：先是目录名称而非内容；
-D：列出文件或目录的更改时间；
-f：在每个文件或目录之前，显示完整的相对路径名称；
-F：在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*"，"/"，"@"，"|"号；
-g：列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码；
-i：不以阶梯状列出文件和目录名称；
-l：<范本样式> 不显示符号范本样式的文件或目录名称；
-l：如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录；
-n：不在文件和目录清单加上色彩；
-N：直接列出文件和目录名称，包括控制字符；
-p：列出权限标示；
-P：<范本样式> 只显示符合范本样式的文件和目录名称；
-q：用“？”号取代控制字符，列出文件和目录名称；
-s：列出文件和目录大小；
-t：用文件和目录的更改时间排序；
-u：列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码；
-x：将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该目录予以排除在寻找范围外。
```
显示当前目录的结构：`$ tree`，等价于 `$ tree .`

显示 /home 目录的结构：`$ tree /home`
<hr>

##### 17. DNS相关
DNS（ Domain Name System ）是域名解析系统，万维网上作为域名和IP地址相互映射的一个分布式数据库，能够使用户更方便的访问互联网，而不用去记住能够被机器直接读取的IP数串。通过域名，最终得到该域名对应的IP地址的过程叫做域名解析（ 或主机名解析）。Linux 系统上 dns 配置文件是`/etc/resolv.conf`

查看 dns 配置文件：`$ cat /etc/resolv.conf`
```python
[root@instance-mtfsf05r ~]# cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 172.16.0.3
nameserver 172.16.0.2
options rotate timeout:1
```
可以通过 nslookup 解析域名：`nslookup www.blueflags.cn`，nslookup 是一个常用的域名查询工具。
<hr>

##### 18. 本地强制dns解析
本地解析配置文件在 /etc/hosts，windows 系统在 C:\Windows\System32\drivers\etc\host，可通过修改该配置文件来配置主机对应的域名。强制 dns 解析常用于开发测试，自定义域名。
```js
[root@instance-mtfsf05r ~]# cat /etc/hosts
 ip                    主机名
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
172.16.0.4 instance-mtfsf05r instance-mtfsf05r.novalocal
106.12.115.131  www.thanlon.cn
```
<hr>

##### 19. 计划任务crond服务
( 1 ) 计划任务
后台运行，到了预定的时间就会自动执行的任务，前提是：事先手动将计划任务设定好，这就用到了 crond 服务。

( 2 ) crond服务相关的软件包
```
[root@instance-mtfsf05r ~]#  rpm -qa | grep cron
cronie-1.4.11-23.el7.x86_64
cronie-anacron-1.4.11-23.el7.x86_64
crontabs-1.11-6.20121102git.el7.noarch
```
这些包在最小化安装系统时就已经安装了，并且会开机自启动 crond 服务，并为我们提供好编写计划任务的 crontab 命令。

( 3 ) crontab命令

crontab 命令被用来提交和管理用户的需要周期性执行的任务，与 windows 下的计划任务类似。

crontab 命令的语法，<kbd>crontab (选项)(参数)</kbd>：
```
-e：编辑该用户的计时器设置；
-l：列出该用户的计时器设置；
-r：删除该用户的计时器设置；
-u<用户名称>：指定要设定计时器的用户名称
```
注意：写计划任务时，命令必须加上绝对路径，否则会出现这种情况：从日志中看，确实触发了计划任务的执行，但是命令却没有执行成功，比如 `* * * * * reboot` 就会出现这种情况，需要将 reboot 写成 /usr/sbin/reboot

查看计划任务的执行：`$ tail -f /var/log/cron`，/var/spool/cron 是存放定时任务的文件。

确保 crontab 服务运行：`$ systemctl status crond`$ ps -ef|grep crond`

检测 crontab 是否开机启动：`$ systemctl is-enabled crond`

查看 crontab 配置文件：`$ cat /etc/crontab `：
```js
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```
crontab 任务配置基本格式：
```python
*  *　 *　 *　 *　　command
分钟(0-59)　小时(0-23)　日期(1-31)　月份(1-12)　星期(0-6,0代表星期天)　 命令

第1列表示分钟0～59 每分钟用*或者 */1表示
第2列表示小时0～23（0表示0点）
第3列表示日期1～31
第4列表示月份1～12
第5列标识号星期0～6（0表示星期天）
第6列要运行的命令

星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
30 08 * * *  每天8.30去上班  
逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。
*/3 * * * * /usr/sbin/ntpdate ntp1.aliyun.com  每隔三分钟执行下时间同步
```
><font>command部分一定要用绝对路径来写。</font>

下面是一些示例：
```python
# 每分钟执行一次命令
* * * * * command
# 每小时的3,15分组执行命令
3,15 * * * * command
# 在上午8-11点的第3和第15分钟执行
3,15 8-11 * * * command
# 每晚21:30执行命令
30 21 * * * command
# 每周六、日的1:30执行命令
30 1 * * 6,0 command
# 每周一到周五的凌晨1点，清空/tmp目录的所有文件(通过which命令查看rm的绝对路径)
0 1 * * 1-5 /usr/bin/rm -rf /tmp/*
# 每晚的21:30重启nginx
30 21 * * * /opt/nginx/sbin/nginx -s reload 
# 每月的1,10,22日的4:45重启nginx
45 4 1,10,22 * * /opt/nginx/sbin/nginx -s reload 
# 每个星期一的上午8点到11点的第3和15分钟执行命令
3,15 8-11 * * 1 command
```
><font>执行多条命令使用 "&&" 来连接不同命令，连续执行多条命令使用 ";" 号。</font>

将 crontab 规则写入到 crontab 文件中：

通过命令 `crontab -e` 打开文件，实际使用的是 vi 打开，然后写入：
```js
* * * * * /usr/bin/echo 'thanlon' >> /tmp/thanlon.txt
```
查看计划任务：`crontab -l`
```python
$ tail -f /tmp/thanlon.txt 
thanlon
thanlon
thanlon
```
通过 `tail -f /tmp/thanlon.txt` 可以看到每分钟向 thanlon.txt 文件追加 “thanlon”
<hr>

##### 20. 软件包管理
( 1 ) 使用 rpm 命令管理，需要手动处理关系依赖。rpm 相关的命令：

安装软件：`rpm -ivh filename.rpm`，i 表示安装，v 表示显示过程，h 表示以进度条显示

升级软件：`rpm -Uvh filename.rpm`

卸载软件：`rpm -e filename.rpm`

查询软件描述信息：`rpm -qpi filename.rpm`

列出软件描述信息：`rpm -qpl filename.rpm`

查询文件属于哪个rpm：`rpm -qf filename`

较小软件的安装可以选择使用 rpm 安装，比如安装 lrzsz：
```python
# 下载lrzsz的rpm包
wget https://rpmfind.net/linux/centos/7.5.1804/os/x86_64/Packages/lrzsz-0.12.20-36.el7.x86_64.rpm
# 安装rpm包
rpm  -ivh lrzsz-0.12.20-36.el7.x86_64.rpm
```
( 2 ) yum命令
yum 命令是在 Fedora 和 RedHat 以及 SUSE 中基于 rpm 的软件包管理器，它可以使系统管理人员交互和自动化地更细与管理RPM软件包，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。

通过 yum 自动安装软件，自动处理依赖关系。yum 安装软件，其实就是在线搜索一个 rpm 包，然后自动 rpm -ivh 包.rpm，比如安装redis：`yum install redis`。yum提供了查找、安装、删除某一个或者一组甚至全部软件包的命令，而且命令简洁而又好记。yum(选项)(参数)
```
-h：显示帮助信息；
-y：对所有的提问都回答“yes”；
-c：指定配置文件；
-q：安静模式；
-v：详细模式；
-d：设置调试等级（0-10）；
-e：设置错误等级（0-10）；
-R：设置yum处理一个命令的最大等待时间；
-C：完全从缓存中运行，而不去下载或者更新任何头文件。
```
部分常用的命令包括：

自动搜索最快镜像插件：`yum install yum-fastestmirror`

查看当前yum仓库有没有mariadb：`yum search mariadb`

卸载软件：`yum remove mariadb-server`

( 3 ) yum源的配置
使用 `cd /etc/yum.repos.d/` 进入 yum.repos.d 目录，目录下的 *.repo 结尾的文件是 yum 源文件。如果我们需要修改 yum 源为 aliyun 的 yum 源仓库，可以到[ **阿里巴巴开源镜像站** ](https://opsx.alibaba.com/mirror )找到 centos 标签点击"帮助"：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191003181618134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

在 Linux 上执行上面的命令：
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
下载阿里巴巴的 yum 源文件。接下来要清空 yum 的缓存：`$ yum clean all`。一般还需要安装 Linux 的额外仓库源，也就是 epel 源，继续在阿里云源上找，找到 epel 标签。在帮助里面找到：
```
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
```
将这一条命令放在 Linux 中执行，安装 epel 源。最后，为了便于加速下载，需要生成 yum 缓存：`yum makecache`
<hr>

##### 21. 系统服务管理命令
系统服务管理命令 systemctl，只有在 centos7 下才有，centos6 中的是 service 命令。centos6 中的 service 的使用：`$ service network start`，`$ service network stop`，`$ service network restart`。

下面是使用 systemctl 命令来管理 redis 服务，，注意使用只有通过 yum 安装的软件，才可以使用 systemctl 命令去管理：

安装 redis：`yum install redis -y`

启动 redis 服务：`systemctl start redis`

重启 redis 服务：`systemctl restart redis`

停止 redis 服务：`systemctl stop redis`

查看 redis 状态：`systemctl status redis`
<hr>

##### 22. xz命令
有时候我们会遇到 xxx.tar.xz 命令，xz 压缩包的格式其实比 7z 要小的多，但是压缩的时间要长一些。其实这种文件采用了两层的压缩，第一层采用 tar 的压缩方式，第二层采用的是 xz 的压缩方式。解压这类文件需要先使用 xz 命令解压所，再使用 tar 命令解压所文件。前面已经介绍过 tar命令，在这里补充说以下xz命令。下面是xz相关的命令：
```python
# 压缩文件到指定路径，不删除原文件，可以指定文件名
xz -c -z tmp.txt > ./tmp.txt.xz
# 例子
thanlon@thanlon-master:~/下载$ xz -c -z eclipse-inst-linux64.tar>./eclipse-inst-linux64.tar.xz
# 解压缩文件，不删除原文件，且可自定义解压后的文件名和文件格式
xz -c -d tmp.txt.xz > tmp.txt
# 直接解压缩filename.tar.xz
tar xvfJ filename.tar.xz
```