![](../imgs/shell/202011060220.jpg)

#### Shell基础
<hr>

##### 1. shell介绍
shell 是一个程序，采用C语言来编写，是用户和 Linux内核 ( kernel ) 沟通的桥梁。<font>它既是一种命令语言，又是一种解释性的编程语言</font>。kernel 负责为软件服务，接受用户或软件指令驱动硬件完成工作。shell 是命令解释器，把用户输入的命令解释为二进制交给 kernel 去工作。
<hr>

##### 2. Shell功能
shell 功能有：命令解释功能；启动程序；输入输出重定向；管道连接；文件名的置换；变量的维护；环境控制；shell 编程。
<font>shell 脚本就是将完成一个任务所有的命令按照执行的前后顺序，自上而下写入到一个文本文件中，然后给予执行权限 。</font>
<hr>

##### 3. 如何写Shell脚本
Shell 脚本的命名。要见名知意，如 nginx 的安装，可以写作 “nginx_install”。名字不要太长，最好控制在 30 个字节以内。虽然 Linux 系统中没有扩展名的概念，但建议还是使用.sh来结尾。

Shell 脚本的格式。<font>Shell 脚本的开头必须指定脚本的运行环境</font>，以 **`#!`** 这个特殊符号组合完成。如 **`#!/bin/bash`** 指定该脚本运行解析由 **`/usr/bin/bash`** 来完成。解释环境的另外一种写法是： **`#!/usr/bin/env bash | python | perl`**。

Shell 脚本注释的使用。Shell 脚本中使用 **`#`** 号来注释文本，注意 **`#!`** 是特例，是专门用来指定脚本的运行环境。一般要带上脚本信息如：
```python
# Author：Nai He
# Created Time：2020/03/29 20:00
# Release：1.0
# Script Description：nginx install script
```
<hr>

##### 4. shell脚本运行方法
Shell 脚本的运行方法有两种，一种是给执行权限运行，另一种是解释器直接运行(不需要给执行权限)。第一种：给脚本一个可执行权限后，找到该脚本的位置运行。第二种：可以使用bash解释器直接执行shell脚本，如`bash nginx_install.sh`。也可以使用sh解释器来执行shell，如`sh nginx_install.sh`，当然还可以使用其它解释器。查看系统中支持的shell解释器：
```py
$ cat /etc/shells 
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
```
<hr>

##### 5. shell特殊符号

|~| ! | $  |  + - * \ %  | &   |  *  |  ?  | ; | \| |\  |  ``| ' |  "|
|--|--|--|--|--|--|--|--|--|--|--|--|--|
| 家目录  | !表示执行历史命令，!!执行上一条命令 | 变量中取内容 |对应数学运算，加减乘除取余| 后台执行  |shell中的通配符，匹配所有  | 匹配一个字符  | 在shell中一行执行多条命令，命令之间用分好分隔 | 管道符，上一个命令的输出作为下一个命令的输入 | 转义字符|  反引号，命令中执行命令|  脚本中字符串用单引号引起来，单引号不解释变量| 脚本中出现字符串可以使用双引号引起来，双引号解释变量 |

<hr>

##### 6. shell管道的应用
管道在 shell 中使用最多，很多组合命令都需要使用管道来完成输出。管道符其实就是下一个命令对上一个命令的输出做处理。
<hr>

##### 7. shell重定向

| > | >> | < | << |
|--|--|--|--|
| 重定向输入，覆盖原数据 | 重定向追加输入，在原数据末尾添加 | 重定向输出 | 重定向追加输出 |

值得注意的是重定向输入内容到同一个文件会先将原文件清空再写入，重定向输入和追加输入都比较常见，也很理解，主要重定向输出。

重定向输出：
```python
# 重定向输出，统计数据流和统计文本
thanlon@thanlon-master:~$ wc < naihe.txt 
 5  5 10
 thanlon@thanlon-master:~$ wc naihe.txt 
 5  5 24 naihe.txt
```
重定向追加输出：
```bash
#!/usr/bin/bash
#Author：Nai He
#Created Time：2020/03/31 08:30
#Script Description：harddisk partition script
fdisk /dev/sdb <<EOF
n
p
3

+100M
w
EOF
```
>问题：有四块硬盘 a、b、c、d，现要求每块硬盘分两个区，每个分区磁盘空间占硬盘空间的1/2，并且一个分区做成LVM-LVM100分区，挂载/data/lv100，另一个分区做成raid5分区，挂载/data/raid5，要求开机挂载。

<hr>

##### 8. shell数学运算
expr 命令只能做整数运算，注意空格：
```python
# 加法
thanlon@thanlon-master:~$ expr 10 + 10
20
# 减法
thanlon@thanlon-master:~$ expr 10 - 10
0
# 乘法
thanlon@thanlon-master:~$ expr 10 \* 10
100
# 除法
thanlon@thanlon-master:~$ expr 10 / 10
1
# $?是返回上一条命令是否执行成功的判断，0表示执行成功，非0表示上一条命令没有执行成功
thanlon@thanlon-master:~$ expr 10 % 10.0
expr: 非整数参数
thanlon@thanlon-master:~$ echo $?
2
thanlon@thanlon-master:~$ expr 10 % 10
0
thanlon@thanlon-master:~$ echo $?
1
thanlon@thanlon-master:~$ expr 10 % 4
2
thanlon@thanlon-master:~$ echo $?
0
# null是系统的回收站，黑洞
# 把命令expr 8 + 8执行的结果输出到/dev/null，用echo $?查看执行成功与否
thanlon@thanlon-master:~$ expr 8 + 8 &>/dev/null;echo $?
0
thanlon@thanlon-master:~$ expr 8 + 8.0 &>/dev/null;echo $?
2
```
使用 let 做计算，也是整数级的运算，注意不要使用空格：
```python
# 加
thanlon@thanlon-master:~$ let sum=10+10
thanlon@thanlon-master:~$ echo $sum 
20
# 减
thanlon@thanlon-master:~$ let a=10-5
thanlon@thanlon-master:~$ echo $a
5
# 乘
thanlon@thanlon-master:~$ let a=10*5
thanlon@thanlon-master:~$ echo $a
50
# 除
thanlon@thanlon-master:~$ let a=10/5
thanlon@thanlon-master:~$ echo $a
2
# 取余
thanlon@thanlon-master:~$ let a=10%5
thanlon@thanlon-master:~$ echo $a
0
```
使用 bc 计算器处理浮点运算，没有该模块可以使用 **`yum install bc`** 来安装：
```python
# 加法
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'.
10+10
20
10 +10
20
10 + 10
20
# 减法
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'.
20-10
10
20-10.3
9.7
# 乘法
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
10*1.0
10.0
# 除法
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
32/20.1
1
# 取余
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
19%29
19
19%29.8
19.0
# 混合运算
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
100*100/100
100
100*(2+3)/10
50
# 支持小数，保留指定位数的小数
thanlon@thanlon-master:~$ bc
bc 1.07.1
Copyright 1991-1994, 1997, 1998, 2000, 2004, 2006, 2008, 2012-2017 Free Software Foundation, Inc.
This is free software with ABSOLUTELY NO WARRANTY.
For details type `warranty'. 
scale=2
100/3
33.33
```
以上都是在交互，<font>shell 是不可以交互的</font>，我们可以举个计算内存使用率的例子，把计算融入到 shell 中：
```python
# 查看内存空间相关
thanlon@thanlon-master:~$ free -mh
              总计         已用        空闲      共享    缓冲/缓存    可用
内存：       7.6Gi       1.8Gi       2.7Gi       538Mi       3.1Gi       5.0Gi
交换：       477Mi          0B       477Mi
# 计算内存的使用率
thanlon@thanlon-master:~$ echo "`echo "scale=2;1.8/7.6*100"|bc`%"
23.00%
# 计算内存的使用率，内部的echo可以使用单引号
thanlon@thanlon-master:~$ echo "`echo 'scale=2;1.8/7.6*100'|bc`%"
23.00%
```
使用 bc 计算器处理浮点运算，scale=2 代表小数点保留两位：
```python
# 加
thanlon@thanlon-master:~$ echo 'scale=2;100+100'|bc
200
# 减
thanlon@thanlon-master:~$ echo 'scale=2;100-100'|bc
0
# 乘
thanlon@thanlon-master:~$ echo 'scale=2;100*100'|bc
10000
# 除
thanlon@thanlon-master:~$ echo 'scale=2;100/100'|bc
1.00
# 取余
thanlon@thanlon-master:~$ echo 'scale=2;100%100'|bc
0
```
双小圆括号运算，在shell中((　))也可以用来做数学运算，注意这种运算也是整数级的运算：
```py
thanlon@thanlon-master:~$ echo $((100+100))
200
thanlon@thanlon-master:~$ echo $((100-100))
0
thanlon@thanlon-master:~$ echo $((100*100))
10000
thanlon@thanlon-master:~$ echo $((100/100))
1
thanlon@thanlon-master:~$ echo $((100%100))
0
# 开方运算
thanlon@thanlon-master:~$ echo $((100**3))
1000000
```
<hr>

##### 9. 脚本退出
exit NUM 退出脚本，释放系统资源，NUM 代表一个整数，代表返回值。注意：<font>NUM 不能太大，它有个范围值 1~255</font>。
```python
# 正常情况下，看上一步是否执行成功，得到结果是０表示成功如
thanlon@thanlon-master:~$ echo $((100+100))
200
thanlon@thanlon-master:~$ echo $?
0
# 当然也可以定义返回的内容，具体操作如下：新建shell文件
thanlon@thanlon-master:~$ vim exit_code.sh
# 在该shell文件中输入以下内容，注意NUM不能太大
#!/bin/bash
echo 'thanlon'
exit 66
# 执行这个shell脚本exit_code.sh，然后看成功的返回值
thanlon@thanlon-master:~$ sh exit_code.sh 
thanlon
thanlon@thanlon-master:~$ echo $?
66
```
##### 10. 添加脚本说明信息
每次写脚本之前，都重新写一次信息说明，不过这样比较麻烦。我们可以打开一个以 <kbd>.sh</kbd> 结尾的新文件时，自动添加这些说明信息。具体操作如下：
```python
# 编辑配置文件，我这里用的是ubuntu示例
thanlon@thanlon-master:~$ sudo vim /etc/vim/vimrc
# 在文件末尾添加下面的内容
autocmd BufNewFile *.sh exec ":call MESS()"
func MESS()
        call append(0,"#!/usr/bin/bash")
        call append(1,"#Description：")
        call append(2,"#Author: thanlon@sina.com")
        call append(3,"#Realease：1.0")
        call append(4,"#Create Time：".strftime("%Y/%m/%d %H:%M"))
endfunc
```
这样每次新建文件，这些信息就会被自动加入：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200412105453517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 11. 格式化输出命令
一个程序有 0 个或 0 个以上的输入，有一个或一个以上的输出。格式化输出命令是 <kbd>echo</kbd>，它的功能是将内容输出到默认的显示设备中，<font>一般起到提示的作用!</font> echo 默认会将输出的字符串送到标准输出，输出的字符串之间用空白字符隔开，并在最后加上换行号。命令选项：

|-n| -e |
|--|--|
| 不在最后自动换行 | <font>若字符串出现需要转义的字符，需要特别加以处理</font>，不会将转义字符当做一般字符输出。 |

```python
thanlon@thanlon-master:~$ echo "Login:";read
Login:
thanlon
thanlon@thanlon-master:~$ echo -n "Login:";read
Login:thanlon
thanlon@thanlon-master:~$ echo "date:";date +%F;
date:
2020-04-02
thanlon@thanlon-master:~$ echo -n "date:";date +%F;
date:2020-04-02
```
转义字符：

| \a | \b | \c | \f | \n | \r | \t | \v | \ | \nnn |
|--|--|--|--|--|--|--|--|--|--|
| 发出警告声 | 删除前一个字符 | 最后不加上换行符号，与命令选项-n相同 | 换行但光标仍停留在原来的位置 | 换行且光标移动到行首| 管标移动到首行，但是不换行 | 插入tab | 与\f相同 | 插入\字符 | 插入nnn(八进制)所代表的ASCII字符 |

<kbd>常用的转义转义字符 \a</kbd>:
```python
thanlon@thanlon-master:~$ echo "\a\a\a\a\a"
\a\a\a\a\a
thanlon@thanlon-master:~$ echo -e "\a\a\a\a\a"

thanlon@thanlon-master:~$ 
```
<kbd>常用的转义字符 \t</kbd>:
```python
thanlon@thanlon-master:~$ echo "thanlon"
thanlon
thanlon@thanlon-master:~$ echo -e "\tthanlon"
	thanlon
```
<kbd>常用的转义字符 \n</kbd>:
```python
thanlon@thanlon-master:~$ echo "\n"
\n
# echo本身输出一个换行，所有有两次换行
thanlon@thanlon-master:~$ echo -e "\n"


thanlon@thanlon-master:~$ 
```
<kbd>常用的转义字符 \b</kbd>:
```python
# 必须在同一行才能删除（不太确定是在转义之前输出了换行）
thanlon@thanlon-master:~$ echo -e "a\b"
a

thanlon@thanlon-master:~$ echo -e -n "a\b"
thanlon@thanlon-master:~$ 
```
<kbd>常用的转义字符 \c</kbd>:
```python
thanlon@thanlon-master:~$ echo -e "thanlon\c"
thanlonthanlon@thanlon-master:~$ echo -n "thanlon"
thanlonthanlon@thanlon-master:~$ echo -n "thanl\con"
thanl\conthanlon@thanlon-master:~$ echo -e "thanl\con"
```
<kbd>应用1：倒计时脚本 time.sh</kbd>：
```bash
#!/usr/bin/bash
# Author：thanlon@sina.com
# Create Time：2020/04/02 14:21
# Release：1.0
# Script Description：countdowm script
for time in `seq 9 -1 0`;do
        echo -n -e "\b$time秒"
        sleep 1
done
echo
```
<kbd>应用2：写一个水果商店的脚本，fruits_shop.sh：</kbd>
```bash
#!/usr/bin/bash
# Author：thanlon@sina.com
# Create Time：2020/04/02 14:21
# Release：1.0

echo -e "\t\tFruits Shop"
echo -e "\t1) Apple"
echo -e "\t2) Orange"
echo -e "\t3) Banana"
echo -n "Which do you chose: "
read
```
<kbd>执行 fruits_shop.sh 输出记录</kbd>：
```python
thanlon@thanlon-master:~$ bash fruits_shop.sh
		Fruits Shop
	1) Apple
	2) Orange
	3) Banana
Which do you chose:
```
<hr>

##### 12. 颜色代码的输出
颜色的格式是：<font>echo -e "\033[背景色:字体颜色 字符串\033[属性效果"</font>

字体颜色范围：30-37
```python
# 在系统中执行才可以看到字体颜色哦
thanlon@thanlon-master:~$ echo -e "\033[30m黑色字体\033[0m"
黑色字体
thanlon@thanlon-master:~$ echo -e "\033[31m红色字体\033[0m"
红色字体
thanlon@thanlon-master:~$ echo -e "\033[32m绿色字体\033[0m"
绿色字体
thanlon@thanlon-master:~$ echo -e "\033[33m黄色字体\033[0m"
黄色字体
thanlon@thanlon-master:~$ echo -e "\033[34m蓝色字体\033[0m"
蓝色字体
thanlon@thanlon-master:~$ echo -e "\033[35m紫色字体\033[0m"
紫色字体
thanlon@thanlon-master:~$ echo -e "\033[36m天蓝色字体\033[0m"
天蓝色字体
thanlon@thanlon-master:~$ echo -e "\033[37m白色字体\033[0m"
白色字体
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200402153541192.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

字体背景颜色：40-47
```python
thanlon@thanlon-master:~$ echo -e "\033[40:35m黑底\033[0m"
黑底
thanlon@thanlon-master:~$ echo -e "\033[40:37m黑底\033[0m"
黑底
thanlon@thanlon-master:~$ echo -e "\033[41:37m红底\033[0m"
红底
thanlon@thanlon-master:~$ echo -e "\033[42:37m绿底\033[0m"
绿底
thanlon@thanlon-master:~$ echo -e "\033[43:37m黄底\033[0m"
黄底
thanlon@thanlon-master:~$ echo -e "\033[44:37m蓝底\033[0m"
蓝底
thanlon@thanlon-master:~$ echo -e "\033[45:37m紫底\033[0m"
紫底
thanlon@thanlon-master:~$ echo -e "\033[46:37m天蓝底\033[0m"
天蓝底
thanlon@thanlon-master:~$ echo -e "\033[47:37m白底\033[0m"
白底
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200402153652213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

最后面的控制选项说明：
```python
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[0m"
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[1m"
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[4m"
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[5m"
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[7m"
thanlon@thanlon-master:~$ echo -e "\033[47:37m\033[8m"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200402151052797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
设置前景色：

| \033[nA  | \033[nB  | \033[nC  | \033[nD  | \033[y;xH  | \033[2J| \033[K | \033[s  | \033[u  | \033[?25l | \033[?25h  |
|--|--|--|--|--|--|--|--|--|--|--|--|
| 光标上移n行 | 光标下移n行 | 光标右移n行 |  光标左移n行| 设置光标的位置 | 清屏 | 清除从光标到行尾的内容 | 保存光标的位置 | 回复光标的位置 | 隐藏光标 | 显示光标 |

<hr>

##### 13. 格式化输入
格式化输入的命令是 read，默认接受键盘的输入，回车符代表输入结束。read命令的相关参数如下所示：

| -p | -t | -s | -n |
|--|--|--|--|
| 打印信息 | 限制时间 | 不回显 | 输入字符的个数 |

示例1：简单使用

<kbd>format_read.sh：</kbd>
```bash
#!/usr/sbin/bash
clear
echo -n 'Login：'
read
echo -n 'Password：'
read
```
<kbd>bash format_read.sh</kbd>：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040608450567.png)

示例2：输出输入的值

<kbd>format_read.sh：</kbd>
```bash
#!/usr/sbin/bash
clear
echo -n 'Login：'
read username
echo -n 'Password：'
read password
echo "The user name and password you input are $username and $password!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406091522666.png)

>如果不使用变量来接受输入的登录的用户名、密码的值，那么输入的值在内存中就定位不到位置（找不到）！

示例3：设置不回显密码

<kbd>format_read.sh:</kbd>
```bash
#!/usr/sbin/bash
clear
echo -n 'Login：'
read username
echo -n 'Password：'
read -s password
echo
echo "The user name and password you input are $username and $password!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040609204144.png)

示例4：设置超时退出时间

<kbd>format_read.sh</kbd>:
```bash
#!/usr/sbin/bash
clear
echo -n 'Login：'
read username
echo -n 'Password：'
read -s -t 10 password # 或者read -s -t 10 password，10秒没输入密码，就会自动退出
echo
echo "The user name and password you input are $username and $password!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406092619631.png)

示例5：设置输入内容的位数限制
```shell
#!/usr/sbin/bash
clear
echo -n 'Login：'
read username
echo -n 'Password：'
read -s -t10 -n6 password # 当输入第6位的时候，自动跳出
echo
echo "The user name and password you input are $username and $password!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406093417393.png)

示例6：使用 -p 参数代替 echo
```bash
#!/usr/sbin/bash
clear
#echo -n 'Login：'
#read username
#代替上面两行代码
read -p "Login：" username
#echo -n 'Password：'
#read -s -t10 -n6 password
read -p "Password：" -s -t20 -n6 password
echo
echo "The user name and password you input are $username and $password!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406093417393.png)

应用：CentOS 7 登录界面
```bash
#!/usr/bin/bash
clear

echo "CentOS Linux 7（Core）"
echo -e "Kernel `uname -r` on `uname -m`\n"
echo -n "$HOSTNAME login："
#还可以使用`uname -n`获取主机名称
#echo -n "`uname -n` login："
read username
read  -p "password：" -s -n6 -t10 password
echo
#echo "The password you input is “$password”!"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200406110740849.png)

>uname -r：获取Linux的版本号；uname -m：获取系统平台；uname -n：获取主机名称

<hr>

##### 14. shell变量
在编程中，我们总有一些数据存放在内存以待后续使用时快速取出来。内存在系统启动的时候被按照1B一个单位编号（16进制编号），并对内存的使用情况做记录，保存在内存跟踪表中。

>变量是编程中最常用的一种临时在内存中存取数据的一种方式。

<hr>

##### 15. 变量分类
① 本地变量：用户私有变量，只有本用户才可以使用，保存在家目录下的 <kbd>.bash_profile</kbd> 或者 <kbd>.bashrc</kbd> 文件中。<font>.bash_profile 中会加载 .bashrc</font> 。

② 全局变量：所有用户都可以使用，保存在 <kbd>/etc/profile</kbd> 或者 <kbd>/etc/bashrc</kbd>，其中 <font>profile 会加载 bashrc</font>，全局变量的生命周期是计算机开机到关机。

③ 用户自定义变量：用户自定义的变量，如终端中写的临时变量，只作用于本终端。还有脚本中定义的变量作用于脚本执行的时候，这些都是用户自定义变量。
>本地变量会在登录成功以后，把变量从文件中加载到内存。而全局变量是在用户登录之前，把所有变量从文件中加载到内存。

<hr>

##### 16. 变量管理
1）定义变量

① 变量的格式是：变量名=值，<font>在 shell 变成中变量名和等号之间不能有空格。</font>

② 变量的命名规则：

- 只能使用英文字母、数字、下划线，首个字符不能是数字

- 中间不能有空格，可以使用下划线

- 不能使用标点符号

- 不能使用bash里的关键字

>字符串要使用单引号或者双引号引起来，可以使用help命令查看保留的关键字。<font>shell 区分大小写，可以把变量名写成大写，就不会和命令冲突了</font>。

2）取消变量

可以在 <kbd>.bashrc</kbd>中定义私有变量，然后使用 source 命令将私有变量加载到内存中，最后使用 <kbd>unset</kbd> 命令取消变量，也就是把变量销毁，释放内存空间。注意前面说过在 <kbd>.bashrc</kbd> 中定义的变量会在下次用户登录成功之后，加载到内存中。
```python
# 编辑.bashrc文件，在文件最后加上：MYSITE='www.thanlon.cn'  
thanlon@thanlon-master:~$ vim ~/.bashrc
# 输出这个变量，因为还没有加载到内存，所以不存在
thanlon@thanlon-master:~$ echo $MYSITE

# 将设置的变量加载到内存
thanlon@thanlon-master:~$ source .bashrc 
# 输出设置的变量MYSITE
thanlon@thanlon-master:~$ echo $MYSITE
www.thanlon.cn
# 取消变量
thanlon@thanlon-master:~$ unset MYSITE
# 检查变量是否还在内存中，发现已不存在
thanlon@thanlon-master:~$ echo $MYSITE

thanlon@thanlon-master:~$
```
>unset 后面直接跟变量名，不要加上$符号!

3）定义全局变量
```python
# 编辑/etc/profile
thanlon@thanlon-master:~$ sudo vim /etc/profile
# 在末尾加上下面给的内容（设置一个全局变量）
export MYSITE='www.thanlon.com'
# 加载到内存
thanlon@thanlon-master:~$ source /etc/profile
# 输出变量
thanlon@thanlon-master:~$ echo $MYSITE
www.thanlon.com
```
>注意：加了 export，$MYSITE 就是一个全局变量，计算机开机就被加载到内存中。但是如果不加 export，那么 $MYSITE 就是一个本地用户变量，在切换用户后变量就没有了！<font>如果在 /etc/profile中设置了全局变量(不加export)，在.bashrc也设置了同名的变量，那么谁最后加载到内存(source)，这个变量就是谁中配置的</font>。

<hr>

##### 17. 数组介绍
变量只能存一个值，但是当需要存储很多个值的时候，定义很多个变量是不可行的，所以引出了数组。数组和变量都可以存数据，不同之处在于数组可以存多个值，而变量只能存一个值。
<hr>

##### 18. 基本数组
数组可以让用户一次赋予多个值，需要读取数据时只需要通过索引调用就可以方便读出来。

( 1 ) 数组语法
```python
数组名称 = (元素1, 元素2, 元素3,...)
```
( 2 ) 数组的读取
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/12 10:51
arr01=('thanlon' 'kiku' 'kili')
echo ${arr01[0]}
echo ${arr01[1]}
echo ${arr01[2]}
```
>同大多数编程语言中的数组一样，数组的索引是从0开始的。

( 3 ) 数组赋值

① 一次赋一个值
```python
array[0]='thanlon'
array[1]='kiku'
array[2]='kili'
```
② 一次赋多个值
```python
array01=(thanlon kiku kili)
array02=(`cat /etc/passwd`)
array03=(`ls /var/ftp/Shell/for*`)
array04=(thanlon kiku kili "bash shell")
```
( 4 ) 数组的查看
```python
# 查看系统中声明过的数组
thanlon@thanlon-master:~$ declare -a
declare -a BASH_ARGC=([0]="0")
declare -a BASH_ARGV=()
declare -a BASH_COMPLETION_VERSINFO=([0]="2" [1]="9")
declare -a BASH_LINENO=()
declare -ar BASH_REMATCH=()
declare -a BASH_SOURCE=()
declare -ar BASH_VERSINFO=([0]="5" [1]="0" [2]="3" [3]="1" [4]="release" [5]="x86_64-pc-linux-gnu")
declare -a DIRSTACK=()
declare -a FUNCNAME
declare -a GROUPS=()
declare -a PIPESTATUS=([0]="0")
```
( 5 ) 访问数组元素
```js
# 查看自定义的数组
echo ${array[0]} $ 访问数组中的第一个元素
echo ${array[@]} $ 访问数组中的所有元素
echo ${#array[@]} $ 统计数组中元素的个数
echo ${!array[@]} $ 获取数组元素的索引
echo ${array[@]:1} $ 从数组下标1开始
echo ${array[@]:1:2} $ 从数组下标1开始访问两个元素
```
( 6 ) 遍历数组
```python
# 默认通过数组元素的个数进行遍历
echo ${arr01[0]}
echo ${arr01[1]}
echo ${arr01[2]}
```
>针对关联数组可以通过数组元素的索引值进行遍历。

<hr>

##### 19. 关联数组
关联数组可以允许用户自定义数组的索引，这样使用起来会更加方便。

( 1 ) 定义关联数组
```python
# 申明关联数组变量
thanlon@thanlon-master:~$ declare -A ass_array01
```
>关联数组用大“A”参数申明，索引数组使用小“a”参数申明。

( 2 ) 关联数组赋值

一次赋一个值：
```python
# 数组名[索引]=变量名
thanlon@thanlon-master:~$ declare -A ass_array01
thanlon@thanlon-master:~$ ass_array01[name]=thanlon
thanlon@thanlon-master:~$ ass_array01[age]=23
thanlon@thanlon-master:~$ echo ${ass_array01[name]}
thanlon
thanlon@thanlon-master:~$ echo ${ass_array01[age]}
23
```
一次赋多个值：
```python
thanlon@thanlon-master:~$ declare -A ass_array2
thanlon@thanlon-master:~$ ass_array2=([name]=thanlon [age]=23)
thanlon@thanlon-master:~$ echo ${ass_array2[name]}
thanlon
thanlon@thanlon-master:~$ echo ${ass_array2[age]}
23
```
( 3 ) 查看数组
```python
thanlon@thanlon-master:~$ declare -A
……
declare -A ass_array01=([age]="23" [name]="thanlon" )
declare -A ass_array2=([age]="23" [name]="thanlon" )
```
( 4 ) 访问数组元素
```js
echo ${ass_array[index]}    # 访问数组某个索引的值
echo ${ass_array[@]} 		# 访问数组中所有元素，等价于echo ${array[*]}
echo ${#ass_array[@]} 		# 获取数组的个数
echo ${!ass_array[@]} 		# 获取数组元素的索引
```
( 5 ) 遍历数组

对于关联数组，<font>需要通过数组元素的索引值进行遍历，不能通过数组元素的编号进行遍历</font>：
```python
thanlon@thanlon-master:~$ echo ${ass_array2[name]}
thanlon
thanlon@thanlon-master:~$ echo ${ass_array2[age]}
23
```
<hr>

##### 20. 职员信息查询系统
<kbd>employee_info.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/12 12:37
for((i=0;i<3;i++))
        do
                read -p "输入第$((i+1))个职员的人名：" employee_name[$i]
                read -p "输入第$[$i+1]个的职员年龄：" employee_age[$i]
                read -p "输入第`expr $i + 1`个职员的性别：" employee_gender[$i]
                echo
done
clear
echo -e "\t\t\t\t职员信息查询系统"
echo "友情提示：输入Q可以直接退出系统！"
while :
        do
                read -p "输入要查询的职员的姓名：" name
                [ $name == "Q" ]&& exit
                flag=0
                for((i=0;i<3;i++))
                        do
                                if [ "$name" == "${employee_name[$i]}" ];then
                                        echo "职员姓名：${employee_name[$i]} 职员年龄：${employee_age[$i]} 职员性别：${employee_gender[$i]}"
                                        flag=1
                                fi
                done
                [ $flag -eq 0 ]&&echo "抱歉，没有找到该职员！"
                echo
done
```
`执行过程`：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200412134830817.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200412134928175.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 21. 数学比较运算
```bash
-eq # 等于
-ge # 大于等于
-gt # 大于
-le # 小于等于
-lt # 小于
-le # 不等于
```
你可以使用 <kbd>man test</kbd> 命令查看：
```python
……
       INTEGER1 -eq INTEGER2
              INTEGER1 is equal to INTEGER2

       INTEGER1 -ge INTEGER2
              INTEGER1 is greater than or equal to INTEGER2

       INTEGER1 -gt INTEGER2
              INTEGER1 is greater than INTEGER2

       INTEGER1 -le INTEGER2
              INTEGER1 is less than or equal to INTEGER2

       INTEGER1 -lt INTEGER2
              INTEGER1 is less than INTEGER2

       INTEGER1 -ne INTEGER2
              INTEGER1 is not equal to INTEGER2
……
```
<font>test</font> 命令结合数学比较运算的简单使用1：
```python
# 如果上一条命令执行成功，返回真就是0，
# test命令只会做判断，不会打印结果，我们需要使用组合命令
thanlon@thanlon-master:~$ test 1 -eq 1;echo $?
0
thanlon@thanlon-master:~$ test 1 -ge 1;echo $?
0
thanlon@thanlon-master:~$ test 1 -le 1;echo $?
0
thanlon@thanlon-master:~$ test 1 -lt 1;echo $?
1
thanlon@thanlon-master:~$ test 1 -gt 1;echo $?
1
thanlon@thanlon-master:~$ test 1 -ne 1;echo $?
1
thanlon@thanlon-master:~$ echo "1.5*10"
1.5*10
thanlon@thanlon-master:~$ echo "1.5*10"|bc
15.0
thanlon@thanlon-master:~$ echo "1.5*10"|bc
15.0
# 15.0以小数点分割，取小数点前面的数，使用f1
thanlon@thanlon-master:~$ echo "1.5*10"|bc|cut -d '.' -f1
15
# 15.0以小数点分割，取小数点后的数，使用f2
thanlon@thanlon-master:~$ echo "1.5*10"|bc|cut -d '.' -f2
0
thanlon@thanlon-master:~$ test `echo "1.5*10"|bc|cut -d '.' -f2` -eq 20;echo $?
1
thanlon@thanlon-master:~$ test `echo "1.5*10"|bc|cut -d '.' -f2` -eq $((2*10));echo $?
1
```
<font>test</font> 命令结合数学比较运算的简单使用2：

<kbd>float.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/13 21:20
num01=`echo "1.5*10"|bc|cut -d "." -f1`
num02=$((2*10))

test $num01 -ge $num02;echo $?
```
`执行过程：`
```python
thanlon@thanlon-master: $ bash float.sh 
1
# -x是查看debug的过程
thanlon@thanlon-master: $ bash -x float.sh 
++ echo '1.5*10'
++ bc
++ cut -d . -f1
+ num01=15
+ num02=20
+ test 15 -ge 20
+ echo 1
1
```
><font>数学运算比较的是整型的数据!</font>

<hr>

##### 22. 文件比较运算
相关参数：

| -d | -e | -f | -r | -s | -w | -x | -O | -G | file1 -nt file2 | file1 -ot file2 |
|--|--|--|--|--|--|--|--|--|--|--|--|
| 检查文件是否存在且为目录 | 检查文件是否存在 | 检查文件是否存在且为文件 | 检查文件是否存且可读 | 检查文件是否存在且不为空 |  检查文件是否存在且可写| 检查文件是否存在且可执行 | 检查文件是否存在且被当前用户拥有 | 检查文件是否存在且默认组为当前用户组 | 检查file1是否比file2新 | 检查file1是否比file2旧 |

应用举例：
```python
# 检查文件是否存在且为目录
thanlon@thanlon-master:~$ test -d /tmp/dir1;echo $?
1
thanlon@thanlon-master:~$ mkdir /tmp/dir1
thanlon@thanlon-master:~$ test -d /tmp/dir1;echo $?
0
# 检查文件是否存在
thanlon@thanlon-master:~$ test -e /tmp/dir1/;echo $?
0
thanlon@thanlon-master:~$ touch /tmp/file
thanlon@thanlon-master:~$ test -e /tmp/file;echo $?
0
# 检查文件是否存在且为文件
thanlon@thanlon-master:~$ test -f /tmp/dir1;echo $?
1
thanlon@thanlon-master:~$ test -f /tmp/file;echo $?
0
# 检查文件是否存且可读
thanlon@thanlon-master:~$ test -r /tmp/dir1;echo $?
0
thanlon@thanlon-master:~$ test -r /tmp/file;echo $?
0
# 检查文件是否存在且不为空
thanlon@thanlon-master:~$ test -s /tmp/dir1;echo $?
0
thanlon@thanlon-master:~$ test -s /tmp/file;echo $?
1
thanlon@thanlon-master:~$ echo "thanlon"> /tmp/file
thanlon@thanlon-master:~$ test -s /tmp/file;echo $?
0
# 检查文件是否存在且可写
thanlon@thanlon-master:~$ test -w /tmp/file;echo $?
0
thanlon@thanlon-master:~$ test -w /tmp/dir1/;echo $?
0
# 检查文件是否存在且可执行
thanlon@thanlon-master:~$ test -x /tmp/dir1/;echo $?
0
thanlon@thanlon-master:~$ test -x /tmp/file;echo $?
1
thanlon@thanlon-master:~$ chmod +x /tmp/file 
thanlon@thanlon-master:~$ test -x /tmp/file;echo $?
0
# 检查文件是否存在且被当前用户拥有
thanlon@thanlon-master:~$ test -O /tmp/file;echo $?
0
thanlon@thanlon-master:~$ test -O /tmp/dir1/;echo $?
0
# 检查文件是否存在且默认组为当前用户组
thanlon@thanlon-master:~$ test -G /tmp/file;echo $?
0
thanlon@thanlon-master:~$ test -G /tmp/dir1/;echo $?
0
# 检查file1是否比file2新
thanlon@thanlon-master:~$ mkdir /tmp/dir1
thanlon@thanlon-master:~$ mkdir /tmp/dir2
thanlon@thanlon-master:~$ stat /tmp/dir1/
  文件：/tmp/dir1/
  大小：4096      	块：8          IO 块：4096   目录
设备：10300h/66304d	Inode：67          硬链接：2
权限：(0755/drwxr-xr-x)  Uid：( 1000/ thanlon)   Gid：( 1000/ thanlon)
最近访问：2020-04-18 11:29:23.380465106 +0800
最近更改：2020-04-18 11:29:23.380465106 +0800
最近改动：2020-04-18 11:29:23.380465106 +0800
创建时间：-
thanlon@thanlon-master:~$ stat /tmp/dir2/
  文件：/tmp/dir2/
  大小：4096      	块：8          IO 块：4096   目录
设备：10300h/66304d	Inode：69          硬链接：2
权限：(0755/drwxr-xr-x)  Uid：( 1000/ thanlon)   Gid：( 1000/ thanlon)
最近访问：2020-04-18 11:29:24.964488303 +0800
最近更改：2020-04-18 11:29:24.964488303 +0800
最近改动：2020-04-18 11:29:24.964488303 +0800
创建时间：-
thanlon@thanlon-master:~$ test /tmp/dir1/ -nt /tmp/dir2/;echo $?
1
thanlon@thanlon-master:~$ test /tmp/dir2/ -nt /tmp/dir1/;echo $?
0
thanlon@thanlon-master:~$ touch /tmp/file1
thanlon@thanlon-master:~$ touch /tmp/file2
thanlon@thanlon-master:~$ stat /tmp/file1
  文件：/tmp/file1
  大小：0         	块：0          IO 块：4096   普通空文件
设备：10300h/66304d	Inode：70          硬链接：1
权限：(0644/-rw-r--r--)  Uid：( 1000/ thanlon)   Gid：( 1000/ thanlon)
最近访问：2020-04-18 11:31:07.855343191 +0800
最近更改：2020-04-18 11:31:07.855343191 +0800
最近改动：2020-04-18 11:31:07.855343191 +0800
创建时间：-
thanlon@thanlon-master:~$ stat /tmp/file2
  文件：/tmp/file2
  大小：0         	块：0          IO 块：4096   普通空文件
设备：10300h/66304d	Inode：71          硬链接：1
权限：(0644/-rw-r--r--)  Uid：( 1000/ thanlon)   Gid：( 1000/ thanlon)
最近访问：2020-04-18 11:31:11.135472107 +0800
最近更改：2020-04-18 11:31:11.135472107 +0800
最近改动：2020-04-18 11:31:11.135472107 +0800
创建时间：-
thanlon@thanlon-master:~$ test /tmp/file1 -nt /tmp/file2;echo $?
1
thanlon@thanlon-master:~$ test /tmp/file2 -nt /tmp/file1;echo $?
0
# 检查file1是否比file2旧
thanlon@thanlon-master:~$ test /tmp/file1 -ot /tmp/file2;echo $?
0
thanlon@thanlon-master:~$ test /tmp/file2 -ot /tmp/file1;echo $?
1
thanlon@thanlon-master:~$ test /tmp/dir1/ -ot /tmp/dir2/;echo $?
0
thanlon@thanlon-master:~$ test /tmp/dir2/ -ot /tmp/dir1/;echo $?
1
# 检查两个文件是否是同一个文件
thanlon@thanlon-master:~$ touch 1.txt 
thanlon@thanlon-master:~$ cp 1.txt /tmp/
thanlon@thanlon-master:~$ test 1.txt -ef /tmp/1.txt ;echo $?
1
# 只有具有相同i节点的才是同一个文件，也就是硬连接，
thanlon@thanlon-master:~$ echo>1.sh
thanlon@thanlon-master:~$ ln 1.sh xxx
thanlon@thanlon-master:~$ ls -il 1.sh xxx 
139968 -rw-r--r-- 2 thanlon thanlon 1 4月  18 11:44 1.sh
139968 -rw-r--r-- 2 thanlon thanlon 1 4月  18 11:44 xxx
thanlon@thanlon-master:~$ test 1.sh -ef xxx ;echo $?
0
```
>注意：新旧比较的是文件的最近更改时间，只有相同i节点的文件才是同一个文件。

<hr>

##### 23. 字符串比较运算
字符串参数解释：

| == | != | -n | -z |
|--|--|--|--|
|等于 | 不等于 | 检查字符串长度是否大于0 | 检查字符串长度是否为0 |

```python
# 比较字符串是否相等
thanlon@thanlon-master:~$ test $USER == "root";echo $?
1
thanlon@thanlon-master:~$ test $USER == "thanlon";echo $?
0
# 比较字符串是否不相等
thanlon@thanlon-master:~$ test $USER != "root";echo $?
0
thanlon@thanlon-master:~$ test $USER != "thanlon";echo $?
1
# 字符串是否不为空/字符串长度大于0
thanlon@thanlon-master:~$ test -n "";echo $?
1
thanlon@thanlon-master:~$ test -n "hahah";echo $?
0
thanlon@thanlon-master:~$ abc=1024
thanlon@thanlon-master:~$ echo "$abc"
1024
thanlon@thanlon-master:~$ test -n "$abc";echo $?
0
# 字符串是否为空/字符串长度是否为0
thanlon@thanlon-master:~$ test -z "";echo $?
0
thanlon@thanlon-master:~$ test -z "hahaha";echo $?
1
thanlon@thanlon-master:~$ abc=1024
thanlon@thanlon-master:~$ echo "$abc"
1024
thanlon@thanlon-master:~$ test -z "$abc";echo $?
1
```
>运算符前后各有一个空格。

<hr>

##### 24. 逻辑运算
逻辑运算参数解释：

|&&| \|\| | !|
|--|--|--|
| 逻辑与运算 | 逻辑或运算 | 逻辑非运算  |

>逻辑与运算、或运算有两个或两个以上的运算，但是逻辑非只有一个运算。

>逻辑与运算：只有条件都为真，结果为真，有一个为假，结果为假。

>逻辑与运算：只要有一个为真，结果为真。

>逻辑非运算：非真即假，非假即真

```python
$ if [ 1 -eq 1 ] && [ 2 -eq 2 ];then echo '真';else echo "假";fi 
真
$ if [ 1 -eq 1 ] && [ 2 -eq 3 ];then echo '真';else echo "假";fi 
假
$ if [ 1 -eq 1 ] || [ 2 -eq 3 ];then echo '真';else echo "假";fi 
真
$ if [ 1 -eq 2 ] || [ 2 -eq 3 ];then echo '真';else echo "假";fi 
假
```
<hr>

##### 25. 赋值运算
```python
$ echo $flag
1
```
>**`= 即表示赋值运算。`**

<hr>

##### 26. 特殊变量

| $* |$# | $$ | $_ | $N | $0|
|--|--|--|--|--|--|--|--|--|
| 脚本的参数| 参数的数量 |脚本执的PID  | 最后执行的命令| 第一个外传参数|脚本的名字|

<kbd>special_variable.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 11:42

echo "脚本的参数：$*"
echo "参数的数量：$#"
echo "脚本执的PID：$$"
echo "最后执行的命令：$_"
echo "第一个外传参数：$1"
echo "脚本的名字：$0"
```
```python
$ bash special_variable.sh thanlon kiku kili
脚本的参数：thanlon kiku kili
参数的数量：3
脚本执的PID：23523
最后执行的命令：脚本执的PID：23523
第一个外传参数：thanlon
脚本的名字：special_variable.sh
```
<hr>

##### 27. 单 if 语句
使用范围是只需要一步判断，单if语句的格式：
```bash
if [ condition ]		#conditon的值为true或者false
    then
        commands
fi
```
<kbd>if_demo1.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 19:38

if [ $USER != "root" ]
    then
        echo "ERROR：need to be root so that"
        exit 1
fi
```
>条件和[]是有空格的，运算符两边也是有空格的。
```python
$ bash if_demo1.sh 
ERROR：need to be root so that
```
<hr>

##### 28. if-then-else
使用与两步判断，条件为真执行什么和条件为假执行什么。if-then-else 的格式：
```bash
if [ conditon ]
    then
        commands1
else
        commands2
fi
```
判断当前用户是否是管理员还是普通用户，如果是管理员输出 “Hello Adminisrator!”，如果是普通用户输出 “Hello Guest!”：

<kbd>if_demo2.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 20:10

if [ $USER == "root" ]
    then
        echo "Hello Adminisrator!"
else
        echo "Hello Guest!"
fi
```
```python
thanlon@thanlon-master:~$ su - root -c "/usr/bin/bash /home/thanlon/工程/ShellProjects/if_demo2.sh"
密码： 
Hello Adminisrator!
thanlon@thanlon-master:~$ su - thanlon -c "/usr/bin/bash /home/thanlon/工程/ShellProjects/if_demo2.sh"
密码： 
Hello Guest!
```
<kbd>num_compare.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 20:41

if [ $1 -gt $2 ]
    then
        echo "$1>$2"
elif [ $1 -lt $2 ]
    then
        echo "$1<$2"
else
        echo "$1=$2"
fi
```
```shell
thanlon@thanlon-master:~$ bash num_compare.sh 1 2
1<2
thanlon@thanlon-master:~$ bash num_compare.sh 2 1
2>1
thanlon@thanlon-master:~$ bash num_compare.sh 1 1
1=1
```
##### 29. if 高级应用
条件括号使用双圆括号，可以在条件中植入数学表达式：
<kbd>num_compare.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 20:41

if (($1>$2))
    then
        echo "$1>$2"
elif (($1<$2))
    then
        echo "$1<$2"
else
        echo "$1=$2"
fi
```
```shell
thanlon@thanlon-master:~$ bash num_compare.sh 1 2
1<2
thanlon@thanlon-master:~$ bash num_compare.sh 2 1
2>1
thanlon@thanlon-master:~$ bash num_compare.sh 1 1
1=1
```
<font>使用双方括号可以在条件中使用通配符</font>，下面例子是匹配 r* 开头的字符串：

<font>square_brackets.sh</font>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 20:56

for var in aa bb cc dd rr rx
    do
        if [[ "$var" == r* ]]
            then
                echo "$var"
        fi
done
```
```bash
thanlon@thanlon-master:~$ bash square_brackets.sh
rr
rx
```
##### 30. for 循环介绍
脚本在执行任务的时候，总是会遇到循环执行的时候，比如说我们需要脚本每隔一段时间执行一次ping操作。除了计划任务可以实现，还可以使用脚本来完成，需要用到循环语句。for循环叫做条件循环，for循环的次数和给予的条件是成正比的。
>在完成同一个任务时，使用循环可以节省内存；并且使用循环是程序结构更清晰；

<hr>

##### 31. for 循环语法
( 1 ) 语法一
```bash
for var in value1 value2 value3……
    do
        commands
done
```
循环输出1~9个数字：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 22:10

for i in `seq 1 9` # 使用命令赋值
    do
        echo $i
done
```
```python
thanlon@thanlon-master:~$ bash for_demo01.sh
1
2
3
4
5
6
7
8
9
```
>seq 1 9 是命令赋值，for var in 1 2 3 4 是直接赋值。还可以赋予字符串，如 for var in thanlon\'s bag, kili\'s bag

```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 22:18

for var in thanlon\'s is cool,
    do
        echo $var
done
```
```python
thanlon@thanlon-master:~$ bash for_demo02.sh 
thanlon's
is
cool,
```
( 2 ) 语法二
```bash
for ((变量;条件;自增减运算))
    do
        代码块
done 
```
循环输出 1~9 个数字：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 22:10

# C格式语法
for((i=0;i<10;i++))
    do
        echo $i
done
```
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 22:18

# 多变量的C格式语法
for((a=0,b=9;a<10;a++,b--)) #a++=a+1,b--=b-1
    do
        echo $a $b
done
```
```python
thanlon@thanlon-master:~$ bash for_demo02.sh 
0 9
1 8
2 7
3 6
4 5
5 4
6 3
7 2
8 1
9 0
```
>使用((;;))条件可以实现无限循环。

编写一个无线循环的脚本：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 00:54

for((;;))
    do
        echo "Hello World!"
done
```
<hr>

##### 32. 循环控制语句
( 1 ) sleep n：脚本执行到某一步休眠n秒

<kbd>sleep.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：监控主机存活的脚本
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 17:01

for((;;))
    do
        ping -c1 $1 &>/dev/null
        if [ $? -eq 0 ]
            then
                echo -e "`date +"%F %H:%M:%S"`:$1 is\033[32m up\033[0m."
        else
                echo -e "`date +"%F %H:%M:%S"`:$1 is\033[32m down\033[0m."
        fi
        #生产环境建议1分钟及以上
        sleep 2
done
```
`执行记录`：
```python
thanlon@thanlon-master:~$ bash sleep.sh 47.102.145.225
2020-04-20 17:18:40:47.102.145.225 is up.
2020-04-20 17:18:42:47.102.145.225 is up.
2020-04-20 17:18:44:47.102.145.225 is up.
```
```python
thanlon@thanlon-master:~$ bash sleep.sh 47.102.145.200
2020-04-20 17:19:24:47.102.145.200 is down.
2020-04-20 17:19:36:47.102.145.200 is down.
2020-04-20 17:19:48:47.102.145.200 is down.
```
>ping 命令的过程如果不想看到，可以使用 &>/dev/null，将标准输出和错误输出都输入到/dev/null，而不显示在控制台上。-c 1或者-c1是限制ping的次数。-w 5或-w5是限制ping的时间。

( 2 ) continue：跳过循环中的某次循环（跳过本次循环）

<kbd>continue.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：continue的简单使用
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 17:41

for((i=0;i<10;i++))
    do
        if [ $i == 2 ];then
            # 本次循环结束，直接进入下一个循环
            continue
        fi
        echo $i
        sleep 1
done
```
`执行过程：`
```python
thanlon@thanlon-master:~$ bash continue.sh 
0
1
3
4
5
6
7
8
9
```
( 3 ) break：跳出循环执行后续代码
```bash
#!/usr/bin/bash
#Description：break用法示例1
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 17:47

for((;;))
    do
        read -p "请输入字符：" ch
        if [ $ch == "Q" ]||[ $ch == "q" ] # ||两边不加空格也是可以的
            then
                break
        else
                echo "您输入的字符是：$ch"
        fi
done
```
`执行过程：`
```python
thanlon@thanlon-master:~$ bash break.sh 
请输入字符：a
您输入的字符是：a
请输入字符：b
您输入的字符是：b
请输入字符：c
您输入的字符是：c
请输入字符：q
```
```bash
#!/usr/bin/bash
#Description：break用法示例2
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 17:47

for((i=0;i<10;i++))
    do
        for((;;))
            do
                echo "Hello thanlon!"
                break     # 跳过本层循环
                #break 1   # 等价于break,跳过本次循环
                #break 2   # 跳过上一层(从内到外第二层)循环
        done
        echo $i
        sleep 1
done
```
`执行过程`：
```shell
thanlon@thanlon-master:~$ bash break.sh 
Hello thanlon!
0
Hello thanlon!
1
Hello thanlon!
2
Hello thanlon!
3
```
( 1 ) 扫描网段中哪些机器是存活的。

( 2 ) 手动写一个同步拉脚本，要求B机器每隔10分钟把A机器的/opt/cache下的内容拉取到B机器的/opt/cache下，并做完整性验证。

( 3 ) 新建user01、user02用户，要求密码是随机6位数，密码取值范围a~z、A～Z、0~9，要求密码不能只是单一的数字或小写或大写字母。

( 4 ) 写一个mysql分库备份脚本

( 5 ) 写一个猜数字脚本，数字范围是1~100，定制计数器，每次才玩都要告诉用户猜大或猜小，如果猜对了跳出脚本并输出计数器的值。

( 6 ) 为DNS写一个自动判断WEB解析的脚本。公司DNS将域名www.thanlon.cn解析到两个web服务器，以实现DNS负载均衡。但是当某个web服务器出现故障，那么DNS依然会将用户解析到宕机WEB，造成不能正常访问。所以要求写一个运行在DNS的检查脚本。当发现哪台WEB宕机后，自己修改DNS的解析记录，关闭对其的解析。当Web恢复，DNS在打开对其的解析并恢复解析。

( 7 ) 写一个九九乘法表。
<hr>

##### 33. while循环介绍
while 和 for一样都是 shell 中负责循环的语句。for是条件循环，是根据条件的个数来决定循环的个数。<font>当我们明确知道循环次数，使用 for。如果不知道循环多少次，使用 while</font>。 
<hr>

##### 34. while循环语法
```shell
while [ condition ]
do
    代码块
done
```
<hr>

##### 35. while循环案例
<kbd>while_demo01.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 22:32

read -p "money：" money
read -p "car_num：" car_num
read -p "house_num" house_num
while [ $money -lt 100000 ]||[ $car -lt 1 ]||[ $house_num -lt 1 ]
    do
        echo "不满足条件！"
        read -p "money：" money
        read -p "car_num：" car_num
        read -p "house_num" house_num
done
echo "满足条件！"
```
**`执行过程`**：
```shell
thanlon@thanlon-master:~$ bash while_demo01.sh 
money：100000
car_num：1
house_num1
满足条件！
thanlon@thanlon-master:~$ bash while_demo01.sh 
money：100000
car_num：0
house_num1
不满足条件！
money：100000
car_num：1
house_num0
不满足条件！
money：100000
car_num：1
house_num1
满足条件！
thanlon@thanlon-master:~$ 
```
<kbd>while_demo02.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 22:36

read -p "Account: " account
while [ $account != "root" ]
do
    read -p "Account: " account
done
```
**`执行过程：`**
```python
thanlon@thanlon-master:~$ bash while_demo02.sh 
Account: thanlon
Account: kili
Account: root
```
<kbd>while_demo03.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 22:48

while [ ! -d /tmp/thanlon/ ]
do
    echo "not found /tmp/thanlon/"
    sleep 2
done
```
**`执行过程：`**
```python
控制台1：
# 控制台2中创建了目录后，脚本执行结束
thanlon@thanlon-master:~$ bash while_demo03.sh
not found /tmp/thanlon/
not found /tmp/thanlon/
not found /tmp/thanlon/
not found /tmp/thanlon/
控制台2：
thanlon@thanlon-master:~$ mkdir /tmp/thanlon/
```
##### 36. while循环控制与语句嵌套
<kbd>while_demo01.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：当i的值为5的时候，停止本循环
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 12:53

i=1
while [ $i -lt 10 ];do
    echo $i
    if [ $i -eq 5 ];then
        break
    fi
    i=$((i+1))
done
```
**`执行过程：`**
```python
thanlon@thanlon-master:~$ bash while_demo01.sh 
1
2
3
4
5
```
<kbd>while_demo01.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 13:03

i=0
while [ $i -lt 10 ];do
        i=$((i+1))
        if [ $i -eq 5 ];then
            continue
        fi
        echo $i
done
```
**`执行过程：`**
```shell
thanlon@thanlon-master:~$ bash while_demo02.sh 
1
2
3
4
6
7
8
9
10
```
>如果代码暂时不用了，可以把把它放到函数中！

<kbd>九九乘法表的实现</kbd>：
```bash
#!/usr/bin/bash
#Description：while实现九九乘法表
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 14:19
i=1
while [ $i -lt 10 ];do
        j=1
        while [ $j -lt $((i+1)) ];do
            echo -n "$j*$i=$((i*j)) "
            j=$((j+1))
        done
    i=$((i+1))
    echo
done
```
```bash
#!/usr/bin/bash
#Description：使用for实现九九乘法表
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 14:19
for((i=1;i<10;i++));do
    for((j=1;j<i+1;j++));do
            echo -n "$j*$i=$((i*j)) "    
    done
    echo
done
```
**`执行过程`**：
```python
1*1=1  
1*2=2 2*2=4 
1*3=3 2*3=6 3*3=9 
1*4=4 2*4=8 3*4=12 4*4=16 
1*5=5 2*5=10 3*5=15 4*5=20 5*5=25 
1*6=6 2*6=12 3*6=18 4*6=24 5*6=30 6*6=36 
1*7=7 2*7=14 3*7=21 4*7=28 5*7=35 6*7=42 7*7=49 
1*8=8 2*8=16 3*8=24 4*8=32 5*8=40 6*8=48 7*8=56 8*8=64 
1*9=9 2*9=18 3*9=27 4*9=36 5*9=45 6*9=54 7*9=63 8*9=72 9*9=81
```
<kbd>使用while遍历文件内容</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 18:47

while read i #读取$1文件，一行一行地赋值给i
    do
        echo "$i" #一行一行地输出
done < $1
```
**`执行过程：`**
```python
thanlon@thanlon-master:~$ bash while_file.sh /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
……
```
<kbd>使用while遍历文件内容，打印列</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/21 18:57

#IFS指定默认的列分隔符
IFS=$":"
while read f1 f2 f3 f4
    do
        echo "$f1 $f2 $f3"
done < /etc/passwd
```
**`执行过程：`**
```python
thanlon@thanlon-master:~$ bash while_file2.sh
root x 0
daemon x 1
bin x 2
sys x 3
sync x 4
games x 5
……
```
>IFS 指定默认的列分隔符!

<hr>

##### 37. until循环介绍
简而言之，until与while相反。until是当条件为假的时候才执行，条件为真则停止执行。
<hr>

##### 38. until语法
```bash
until [ condition ] # 注意条件为假until才会循环，条件为真，until停止循环
do
    代码块
done
```
<hr>

##### 39. until案例
<kbd>until01.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：until案例1
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 18:59

num=10
until [ $num -gt 20 ];do
    echo $num
    num=$((num+1))
    sleep 0.5
done
```
`执行过程:`
```shell
thanlon@thanlon-master:~$ bash until01.sh 
10
11
12
13
14
15
16
17
18
19
20
```
<kbd>until02.sh</kbd>：
```bash
#!/usr/bin/bash
#Description：while与until综合应用，数字接龙
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/20 18:59

num=1
while [ $num -lt 5 ];do
    echo $num
    sleep 0.1
    num=$((num+1))
    until [ $num -lt 5 ];do
        echo $num
        sleep 0.1
        if [ $num -eq 9 ];then
            break
        fi
        num=$((num+1))
    done
done
```
**`执行过程:`**
```shell
thanlon@thanlon-master:~$ bash until02.sh 
1
2
3
4
5
6
7
8
9
```
<hr>

##### 40. case介绍
case 语句是多条件分之语句。在生产环境中，我们会遇到一个问题需要根据不同的情况来执行不同的预案，那么我们要处理这些问题就要首先根据可能出现的情况写出对应的预案，根据出现的情况来加载不同的预案。实际中也有这样的例子，**`如监控内存的使用率`**。
<hr>

##### 41. case语法
```bash
case 变量 in
条件1)
			执行代码块1
;;
条件2)
			执行代码块2
;;
……
esac
```
>每个代码块执行结束要以;;结尾代表结束。case结尾要以反转的esac来结束。

相关案例：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 13:36

read -p "请输入您要查找的职工姓名：" N
case $N in
Thanlon)
    echo "1.姓名：Thanlon"
    echo "2.性别：男"
    echo "3.年龄：23"
;;
Kili)
    echo "1.姓名：Kili"
    echo "2.性别：女"
    echo "3.年龄：21"
;;
Kiku)
    echo "1.姓名：Kiku"
    echo "2.性别：女"
    echo "3.年龄：24" 
;;
esac
```
```python
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh 
请输入您要查找的职工姓名：Thanlon
1.姓名：Thanlon
2.性别：男
3.年龄：23
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh 
请输入您要查找的职工姓名：Kili
1.姓名：Kili
2.性别：女
3.年龄：21
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh 
请输入您要查找的职工姓名：Kiku
1.姓名：Kiku
2.性别：女
3.年龄：24
```
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 14:02

case $1 in
nn|NN)
    echo "奶奶好！"
;;
lzr|LZR)
    echo "伯父好！"
;;
zmn|ZMN)
    echo "伯母好！"
;;
*)
    echo "USAGE：$0 nn|lzr|zmn"
;;
esac
```
```shell
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh
USAGE：case_demo.sh nn|lzr|zmn
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh nn
奶奶好！
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh lzr
伯父好！
thanlon@thanlon-master:~/工程/ShellProjects$ bash case_demo.sh zmn
伯母好！
```
<hr>

##### 42. 函数介绍
很多人在写代码的时候，都是习惯从头写到结束，完成后再一起测试。但是测试阶段发现错误非常多。为了解决这个问题，建议将代码优化，一个模块实现一个功能，哪怕一个很小的功能都可以。这样的话，我们的代码就会逻辑上比较简单，代码量少，拍错简单。函数的优点是：<font>调用方便，节省内存；代码量少，排错简单；可以改变代码的执行顺序</font>。
<hr>

##### 43. 函数语法
函数有两种语法：
```bash
# 第一种语法
函数名(){
	代码块
}
# 第二种语法
function 函数名{
	代码块
}
```
<kbd>第一种语法 shell 示例</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 02:19

#定义函数
start(){
        echo "Apache start...           [OK]"
        return 0 # 返回一个值，可写可不写
}

stop(){
        echo "Apache stop...            [FAIL]"
}
#调用函数
start
stop
```
<kbd>第二种语法shell示例</kbd>：
```bash
#!/usr/bin/bash
#Description：
#Author: thanlon@sina.com
#Realease：1.0
#Create Time：2020/04/19 02:19

#定义函数
function start {        #不要忘了函数名{之间空一格 
        echo "Apache start...           [OK]"
        #return 0
}

function stop {
        echo "Apache stop...            [FAIL]"
}
#调用函数
start
stop
```
<hr>

##### 44. nginx启动管理脚本
请确保您的电脑已安装 nginx，我这里将 nginx 安装在 CentOS 7 系统中。

<kbd>nginxd.sh</kbd>
```bash
#!/usr/bin/bash

#参数
#nginx的安装目录
nginx_install_doc=/usr/local/nginx1180 #实际上就是字符串
#nginx的启动命令
nginxd=$nginx_install_doc/sbin/nginx
#nginx存放PID的文件
pid_file=$nginx_install_doc/logs/nginx.pid
#自定义变量用来代替nginx
proc=nginx

#调用系统提供的函数库
. /etc/init.d/functions #.就是source
#判断有无函数库文件
if [ -f /etc/init.d/functions ];then
    . /etc/init.d/functions
else
    echo "not found file /etc/init.d/functions"
fi	
#if [ -f $pid_file ];then
    #nginx_process_id=`cat $pid_file` 
    #nginx_process_num=`ps aux|grep proc|grep -v "grep"|wc -l`
#fi
#获取nginx进程数
nginx_process_num=`ps aux|grep proc|grep -v "grep"|wc -l`

#启动nginx函数
start(){
if [ -f $pid_file ]&&[ $nginx_process_num -ge 1 ];then
    echo "nginx is running..."
else
    if [ -f $pid_file ]&&[ $nginx_process_num -lt 1 ];then
        rm -rf $pid_file #启动失败或断电等导致pid文件没有被删除,正常情况是nginx退出pid文件就被删除
        #echo "nginx started.`daemon $nginxd`" #启动nginx程序,daemon函数来自函数库
        action "nginx started." $nginxd
    fi
    #echo "nginx started`daemon $nginxd`"
    action "nginx started." $nginxd
fi
}

#停止nginx函数
stop(){
if [ -f $pid_file ]&&[ $nginx_process_num -ge 1 ];then
    action "nginx stoped" killall -s QUIT $proc
    rm -f $pid_file
else
    action "nginx stoped." killall -s QUIT $proc 2>/dev/null
fi
}

#重启nginx函数
restart(){
    stop
    sleep 1 # 确保上一步能完成
    start
}

#重载nginx
reload(){
if [ -f $pid_file ]&&[ $nginx_process_num -ge 1 ];then
    action "nginx reloaded." killall -s HUP $proc
else
    action "nginx reloaded." killall -s HUP $proc 2>/dev/null
fi
}

#nginx状态
status(){
if [ -f $pid_file ]&&[ $nginx_process_num -ge 1 ];then
    action "nginx running..."
else
    action "nginx stoped."
fi
}

case $1 in
start)start;;
stop)stop;;
restart)restart;;
reload)reload;;
status)status;;
*)echo "$USAGE:$0 start|stop|restart|reload|status";;
esac
```
`脚本执行步骤：`
```python
[root@test-14 ~]$ ./nginxd.sh start
nginx started.                                             [  确定  ]
[root@test-14 ~]$ ./nginxd.sh start
nginx is running...
[root@test-14 ~]$ ./nginxd.sh stop
nginx stoped                                               [  确定  ]
[root@test-14 ~]$ ./nginxd.sh stop
nginx stoped.                                              [失败]
[root@test-14 ~]$ ./nginxd.sh start
nginx started.                                             [  确定  ]
[root@test-14 ~]$ ./nginxd.sh restart
nginx stoped                                               [  确定  ]
nginx started.                                             [  确定  ]
[root@test-14 ~]$ ./nginxd.sh reload
nginx reloaded.                                            [  确定  ]
[root@test-14 ~]$ ./nginxd.sh status
nginx running...                                           [  确定  ]
[root@test-14 ~]$ ./nginxd.sh stop
nginx stoped                                               [  确定  ]
[root@test-14 ~]$ ./nginxd.sh status
nginx stoped.          
```
```python
# 可以将脚本复制到/etc/init.d/下并命名为nginxd，让其可以被当作系统服务管理脚本用service命令执行
[root@test-14 ~]$ cp nginxd.sh /etc/init.d/nginxd
```
>可以使用 killall 命令杀掉 nginx，杀掉 nginx 之后 pid 文件就没有了。

<hr>

##### 45. 正则表达式介绍
正则表达式是一种文本模式的匹配，包括普通字符（如a到z之间的字母）和特殊字符（元字符）。它是一种字符串匹配模式，可以用来检查一个字符串是否包含某种子串替换或者从某个字符串中取出某个条件的子串。正则表达式是第三方产品，很多编程语言都支持，<font>shell 也是支持的。但是不是所有的 shell 命令都是支持正则表达式的。常见的命令中只有 grep、sed、awk 命令支持正则表达式</font>。

数据源（下面做匹配要用到）：

<kbd>file</kbd>
```shell
ac
ab
abbc
abcc
aabbcc
abbbc
abbbbbc
acc
abc
asb
aa
bb
a_c
zZc
aAAAAc
a c
ABC

ccc

dddd
http://www
abababab
c c d
123
a3c
e*f
```
>nginx也支持正则表达式。

<hr>

##### 46. 特殊字符

|定位符| 说明 |
|--|--|
| ^ | 锚定开头^a是以a开头，默认锚定一个字符 |
| $ | 锚定结尾a$是以a结尾，默认锚定一个字符 |

>定位符：同时锚定开头和结尾，做精确匹配。如果是单一锚定开头和结尾，做模糊匹配。

( 1 ) 精确匹配和模糊匹配
```python
# 匹配以a开头，以c结尾
thanlon@thanlon-master:~$ egrep "^ac$" file 
ac
```
```python
# 匹配以a开头
thanlon@thanlon-master:~$ egrep "^a" file 
ac
ab
abbc
abcc
aabbcc
abbbc
abbbbbc
acc
abc
asb
aa
a_c
aAAAAc
a c
abababab
a3c
a*c
bc
```
```python
# 匹配以a结尾
thanlon@thanlon-master:~$ egrep "a$" file 
aa
```
( 2 ) 匹配字符串

| .| 匹配除了回车以外的任意字符 |
|--|--|
| ( ) | 字符串分组 |
| [ ] | 和.一样匹配任意字符，但[]可以定义取某个范围内的字符 |
| [^] | 表示否定 |
| \ | 转义字符 |
| \| |管道，或的意思|

```python
# .
thanlon@thanlon-master:~$ egrep "^a.c$" file 
acc
abc
a_c
a c
a3c
```
```python
# []
thanlon@thanlon-master:~$ egrep "^a[0-9]c$" file 
a3c
```
```python
# 在[]加^表示取反
thanlon@thanlon-master:~$ egrep "^a[^0-9]c$" file 
acc
abc
a_c
a c
thanlon@thanlon-master:~$ egrep "^a[^a-z]c$" file 
a_c
a c
a3c
```
```python
# 转义字符，*号在正则表达始终是有意义的，所以
thanlon@thanlon-master:~$ egrep "^a\*c$" file 
a*c
```
```python
# 分组()和管道|
thanlon@thanlon-master:~$ egrep "^(a|b)c$" file 
ac
bc
```
>当[没有设定分组的时候，.和[]是同一个意思。

( 3 ) 限定符，对前面的字符或者字符串做限定说明

| 限定符 | 说明 |
|--|--|
|*  | 某个字符加*号表示该字符不会出现或出现多次 |
| ？ | 与*号相似，但是该字符出现一次或不出现 |
| + | 与*号相似，表示其前面字符出现一次或多次，但必须出现一次 |
| {n,m} | 某个字符之后出现，表示该字符最少n次，最多m次 |
| {m} | 正好出现m次 |

测试案例：
```python
# 某个字符加*号表示该字符不会出现或出现多次
thanlon@thanlon-master:~$ egrep "^ab*c$" file 
ac
abbc
abbbc
abbbbbc
abc
```
```python
# 某个字符加?号表示该字符出现一次或不出现
thanlon@thanlon-master:~$ egrep "^ab?c$" file 
ac
abc
```
```python
# +与*号相似，表示其前面字符出现一次或多次，但必须出现一次
thanlon@thanlon-master:~$ egrep "^ab+c$" file 
abbc
abbbc
abbbbbc
abc
```
```python
# {n,m}某个字符之后出现，表示该字符最少n次，最多m次 
thanlon@thanlon-master:~$ egrep "^ab{1,6}c$" file 
abbc
abbbc
abbbbbc
abc
```
```python
# 正好出现m次
thanlon@thanlon-master:~$ egrep "^ab{2}c$" file 
abbc
thanlon@thanlon-master:~$ egrep "^ab{1}c$" file 
abc
```
##### 47. 正则表达式POSIX字符
