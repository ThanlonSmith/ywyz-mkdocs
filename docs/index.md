![](imgs/linux/linux-basis/202011050000.jpeg)
#### Linux基础
<hr>

##### 1. 多虚拟终端
可以使用 ctrl + alt + f [1~7] 命令切换终端。
<hr>

##### 2. 目录结构
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923165154517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

<kbd>/home</kbd>：普通用户家目录

<kbd>/root</kbd>：超级用户的家目录

<kbd>/etc</kbd>：存放软件的配置文件。一般来说，通过yum(pip)自动安装的软件，配置文件一般都在/etc下。
mysql的配置文件在/etc/my.conf，redis的配置文件在/etc/redis.conf

<kbd>/sbin</kbd>：存放可执行命令文件

<kbd>/bin</kbd>：存放可执行命令文件

<kbd>/usr/local/bin</kbd>`：存放可执行命令文件

<kbd>/opt</kbd>：存放额外安装的软件目录。比如我们要安装nginx和python3软件，正规的存放目录是/opt

<kbd>/tmp</kbd>：存放临时文件，不重要的文件、文件夹

<kbd>/var</kbd>：存放系统日志文件居多，比如存放nginx，python，django等等日志
>**Linux 下绿色文件代表可执行文件，蓝色文件代表文件夹，白色文件代表普通文件。**

<hr>

##### 3. 文件系统
用户在硬件存储设备中执行的文件建立，写入，读取，修改，转存与控制等操作都是依赖文件系统完成的。<font>文件系统的作用是：合理规划硬盘，保证用户正常使用。</font>

Linux系统支持数十种文件系统，常见文件系统如下：

( 1 ) Ext3： 是一款日志文件系统，能够在系统异常宕机时避免文件系统资料丢失，并能自动修复数据的不一致与错误。

( 2 ) Ext4： Ext3 的改进版本，作为 RHEL 6 系统中的默认文件管理系统，它支持的存储容 量高达 1EB（ 1EB = 1,073,741,824GB ），且能够有无限多的子目录。另外，Ext4 文件系统能够批量分配 block 块，从而极大地提高了读写效率。

( 3 ) XFS：是一种高性能的日志文件系统，而且是 RHEL 7 中默认的文件管理系统。它的优势在发生意外宕机后尤其明显，即可以快速地恢复可能被破坏的文件，而且强大的 日志功能只用花费极低的计算和存储性能。并且它最大可支持的存储容量为 18EB， 这几乎满足了所有需求。XFS 文件系统对云计算、容器虚拟化支持性特别好。

**`cat /etc/fstab`**：检查 Linux 的文件系统。fstab是用来存放文件系统的静态信息的文件
><font>面试的时候很可能让你写下来!</font>

<hr>

##### 4. 命令的组成格式
```sql
[root@instance-mtfsf05r ~]# 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190923154438102.png)

<hr>

##### 5. 目录的创建
普通的目录创建：**`mkdir a`**，创建一个a文件夹

递归的创建：**`mkdir -p a/b c/d`**，在当前目录创建一个a文件夹和c文件夹，并且a文件中和c文件夹中分别有b和d文件夹。

递归创建几个文件夹：**`mkdir -p ./test/{a,b,c,d}`**，创建test文件夹，里面有 a，b，c，d四个文件夹。直接创建 a，b，c，d 四个文件夹，可以使用 **`mkdir {a,b,c,d}`**，当然这不是递归创建了，记得有这样的语法格式。

<hr>

##### 6. 文件的创建
创建文件：**`touch xxx.txt`**，如果xxx.txt文件存在，再次使用这个指令会更改文件的创建(或修改)时间

修改文件的更改时间戳：**`touch -t '09280920' xxx.txt`**
<hr>

##### 7. 查看文件的详细信息
显示文件详细信息：**`stat  xxx.txt`**，使用此命令可以看到文件的权限、最近访问时间、最近更改时间、最近改动时间等信息。如果 touch 一下这个命令，最近访问时间、最近更改时间和最近改动时间都会发生变化，且变成相同的时间。如果使用 touch -t 更改时间戳，最近访问时间和最近更改时间改成时间戳的时间，但是最近改动时间不能改成指定的时间戳。 

显示文件权限：**`stac -c %a xxx.xt`** **`stac -c %A xxx.txt`**
```python
thanlon@plus-book:~$ stat -c %a 1.txt 
644
thanlon@plus-book:~$ stat -c %A 1.txt 
-rw-r--r--
```
<hr>

##### 8 vim编辑器
先看一下vi编辑器的用法，

打开文件：**`vi test.py`**，使用此命令后，进入命令模式，等待用户输入命令。用户可以使用i o a进入编辑模式。向文件中写完内容后，回到底线命令模式(末行模式)，可以输入命令保存和退出。
><font>所有的类 Unix 的系统都会内建 vi 文本编辑器，其它的文本编辑器则不一定存在。但是目前我们使用比较多的是 vim 编辑器。vim 具有程序编辑的功能，可以主动地以数字颜色辨别语法的正确性，方便程序设计。相对于 vi，vim 可以自定义快捷键，也提供语法检测。</font>

<hr>

##### 9 查看文件内容
( 1 ) cat命令

查看文件显示行号：`cat -n xxx.py`

查看文件：`cat xxx.py`

在每一行的结尾加上$符号：`cat -E xxx.py`，可以扩展 `cat -En xxx.py`

追加内容到文件中：
```js
thanlon@plus-book:~$ cat >> love.txt << EOF
> THANLON
> LOVE
> KIKU
> EOF
thanlon@plus-book:~$ cat -En love.txt 
     1	THANLON$
     2	LOVE$
     3	KIKU$
```
cat 只适合查看比较短的文本，cat命令执行后会在内从中读取内容，展示在终端上。如果是一个超大的文件，可能会占用很大内存。如果我们cat一下二进制命令，如`cat /usr/bin/ls`，就会占用很多内存(可执行命令是已经被编译成机器码的那种命令)。看长文本使用more命令。

( 2 ) more命令

查看文本会以百分比形式告知已经看了多少内容，命令是 `more /etc/passwd`，使用回车键向下读取内容。按空格是翻页，按 b 是上一页。
<hr>

##### 10. 常用快捷键
tab：自动补全命令、文件夹和目录名
logout：退出当前会话
ctrl + l/clear/cls：清楚终端显示
ctrl + c：中止当前操作
<hr>

##### 11. 输出内容到终端
默认显示在终端上：`echo 'thanlon'`

将信息写入文件中：`echo 'kiku' > /tmp/test.txt`，`echo 'kiku' >> /tmp/test.txt`，一个是覆盖写，一个是追加写。

<hr>

##### 12. 特殊符号
追加重定向，把内容追加到文件的结尾：`>>`，理解为 with open 的 a 模式

重定向符号，清空原文件的所有内容：`>`，然后把内容覆盖到文件中，理解为 with open 的 w 模式

输入重定向，把命令信息写到前面的命令`<`

将输入结果输入重定向`<<`

<hr>

##### 13. 文件的复制
将文件拷贝到某目录下：`cp xxx.py tmp/`

拷贝文件：`cp xxx.py tmp.py`

拷贝文件顺便改名：`cp xxx.py tmp/test.py`

递归复制目录以及目录的子孙后代：`cp -r xxx1 xxx2`

复制文件并且保持文件的属性不变：`cp -p`

操作文件前，先备份：`cp test.py test.py.back`

<hr>

##### 14. 文件的移动
把文件移动到文件夹中：`mv old.txt tmp/`

修改文件的名字，重命名：`mv old.txt new.txt`

<hr>

##### 15. 文件的删除
删除空的目录：`rmdir tmp`

需要删除的确认：`rm -i test.py`，等价于`rm test.py`，这里使用了 alias。这是一个安全机制，防止失误操作。

强制删除：`rm -f test.py`

递归删除目录和目录中的文件：`rm -r tmp`

强制删除目录及子目录以及目录中的所有文件：`rm -rf tmp`

<hr>

##### 16. 查找命令
按照文件名查找文件：`find / -name xxx.txt`，还可以用*号匹配 `find / -name *.txt`

查找某一类型的文件：`find / -type xxx.txt`，默认类型是目录和文件。type的相关的参数有b：块设备文件；d：目录；c：字符设备文件；p：管道文件；l：符号链接文件；f：普通文件；s：socket 文件

找到/tmp下所有以.txt 结尾的文件：`find /tmp/ -type f -name *.txt`

找到/etc下所有名字以host开头的文件：`find /etc/ -name host*`

<hr>

##### 17. 管道命令
Linux 提供的管道符号 "|" 将两条命令隔开，管道符左边命令的输出会作为管道复右边命令的输入：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190928163546397.png)

常见的使用方法：

检查python程序是否启动：`ps -ef|grep python`，ps -ef 是查看进程，通过 grep 过滤与 python 有关的进程

找到/tmp目录下所有txt文件：`ls /tmp|grep '.txt'`

检查ngnix的端口是否存活：`netstat -tunlp|grep nginx`，netstat -tuple是查看端口，可以通过管道符号加grep过滤条件进行筛选

<hr>

##### 18. grep命令
grep 是 global search regular expression(RE) and print out the line ( 全面搜索正则表达式并把行打印出来 ) 的英文简写。它是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

语法：grep [参数] [--color=auto] [字符串] filename

参数 -i 是忽略大小写，-n 是输出行号，-v 是反向选择，--color=auto 是给关键词部分添加颜色（ 默认 ）

在文件中找某些内容，`grep 'xxx' test.txt`，如：`grep 'allow_host' settings.py`。也可以输出行号，`grep -n 'allow_host' settings.py`。也可以反向选择，`grep -nv 'allow_host' settings.py`表示查找除了'allow_host'的那一行。

找到以 # 开头的内容：`grep '^#' redis.conf`

过滤掉以 # 开头的内容：`grep -v '^#' redis.conf`

过滤掉空行：`grep -v '^$' redis.conf`，^ $表示以什么开头以什么结尾

<hr>

##### 19. head和tail命令
head：显示文件前n行，默认前10行

tail： 显示文件后n行，默认后10行

查看前两行：`head -2 tmp.txt`或`head -n 2 tmp.txt`

查看后两行：`tail -2 tmp.txt`或`tail -n 2 tmp.txt`

持续刷新显示：`tail -f tmp.txt`，如`tail -f /var/log/mysql.log`常用于对日志文件进行监控，监测日志实时写入的信息。这个写入是一个追加

显示文件10～30行：`tail -n 30 tmp.txt | tail -n 21`

<hr>

##### 20. sed命令
sed命令和grep命令一样用于对文本进行过滤，还可以用于修改文本。sed命令的参数有，s表示替换指令，d表示删除指令，g表示全局替换。

找到文件中的root字符串，全局替换为thanlon的命令：`sed 's/root/thanlon/g'  tmp.txt` 但是不会写入到文件，只会返回替换结果。

找到文件中的root字符串，全局替换为thanlon，写入到文件中的命令：`sed 's/root/thanlon/g'  tmp.txt`。-i参数：写入到文件，不会返回命令执行结果，直接生效。

找到第2行删除：`sed -i '2d' tmp.txt`

删除文件中的空白行：`sed -i '/^$/d' tmp.txt`，^ 和 $ 分别表示以什么开头和以什么结尾。

<hr>

##### 21. alias命令
使用alias可以给命令添加别名，

给rm添加提示：`alias rm="echo '你不可以使用rm命令'"`

取消rm的别名：`unalias rm`

<hr>

##### 22. which命令
which 命令用于查找命令的绝对路径，环境变量$ PATH中保存了查找命令时需要遍历的目录。which 指令会在环境变量$PATH设置的目录里查找符合条件的文件。也就是说使用 which 命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的孩子令。相似还有 whereis 和 whoami。

查找cd的路径：`which python`，终端打印：/usr/bin/cd

查找python的安装路径：`whereis python`

查看linux上所有用户：`who`

查看当前用户：`whoami`

<hr>

##### 23. scp命令
scp命令用于linux之间复制文件和目录的命令，scp是secure copy的缩写, scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令。参数-r：递归复制整个目录；-v：详细方式输出，比如传输过程中输出debug信息；-q：不显示传输进度条；-C：允许压缩。

传输本地文件到远程主机：`scp 本地文件  远程用户名@远程ip:远程文件夹/`

传输本地文件夹到远程主机：`scp -r 本地目录 远程用户名@远程ip:远程文件夹/`

复制远程主机文件到本地：`scp 远程用户名@远程ip:远程目录/远程文件 本地目录/`

复制远程主机目录到本地：`scp -r 远程用户名@远程ip:远程目录/ 本地目录/`
```python
# 将本地Nginx目录上传到远程主机的/root/目录下，修改目录名为ssl (ssl目录没有会被创建)
scp -r Nginx/ root@106.12.115.136:/root/ssl
# 将本地Nginx目录上传到远程主机的/root/ssl/目录下，注意与上面的区别
scp -r Nginx/ root@106.12.115.136:/root/ssl/
```
<hr>

##### 24. du命令
du命令是用来显示目录或文件的大小，会显示指定目录或文件所占用的	磁盘空间。参数有-s：显示统计，-h是以K,M,G为单位显示，可读性强。

查看/var/log/目录的大小：`du -sh /var/log/`

查看文件的大小：`du -h tmp.txt`

请写出如何统计/var/log/django文件夹的大小：`du -sh /var/log/django`，<font>面试题注意了。</font>

<hr>

##### 25. top命令
命令用于动态地监视进程活动与系统负载等信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190929081338593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

第一行：uptime，系统时间、主机运行时间、用户连接数(who)、系统每1分钟，5分钟，15分钟的平均负载。

第二行：描述进程信息。进程总数、正在运行的进程数、睡眠的进程数、停止的进程数、僵尸进程数。

第三行：描述 cpu 信息。0.2us是用户空间所占CPU百分比、0.3sy是内核空间占用CPU百分比、0.0ni是用户进程空间内改变过优先级的进程占用CPU百分比、99.5 id是空闲CPU百分比。0.0 wa是等待输入输出的CPU时间百分比、0.0 hi是硬件CPU中断占用百分比、0.0 si是软中断占用百分比、0.0 st是虚拟机占用百分比。

<hr>

##### 26. chattr命令
给文件加锁，只能写入数据，无法删除文件。

给文件加锁：`chattr +a tmp.txt`

给文件减锁：`chattr -a tmp.txt`

<hr>

##### 27. lsattr命令
查看文件隐藏属性：`lsattr tmp.txt`
<hr>

##### 28. 时间同步
Linux 的 date 可以显示当前系统时间或者设置系统时间。-d 参数(--date=string)显示指定时间而不是当前时间。在 Linux 下系统时间和硬件时间不会自动同步，在 Linux 运行过程中，系统时间和硬件时间以异步方式运行，互不干扰。硬件时间的运行，是靠Bios电池来运行，而系统时间是用CPU tick来维持。在系统开机的时候，会从Bios中获取硬件时间，设置为系统时间。

格式化输出：`date +"%Y-%m-%d"` 表示以“年-月-日”的格式显示当前时间 `hwclock` 表示以“年-月-日 十分秒”的格式显示当前时间。

硬件时钟的查看：`hwclock`

同步系统时间和硬件时间：`hwclock -w`表示以系统时间为基准，修改硬件时间。`hwclock -s`表示以硬件时间为基准修改系统时间。

将当前系统时间与 <font>时间服务器</font>（NTP）同步：`ntpdate -u ntp.aliyun.com`，u表示更新。

<hr>

##### 29. wget命令
从网络中下载资源：`wget https://www.baidu.com/`

递归下载资源：`wget -r -p https://www.baidu.com/`

<hr>

##### 30. 开关机命令
重启机器：`reboot`

关闭机器：`poweroff`

<hr>

##### 31. apt命令
apt 是 Advanced Packaging Tool, 是 Ubuntu 下的安装包理工具，ubuntu 下大部分的软件安装/更新/卸载都是利用apt命令来 实现的。

安装软件：`sudo apt install 软件名`

卸载软件：`sudo apt remove 软件名`

更新可用软件包列表：`sudo apt update`

更新已安装的包：`sudo apt upgrade`