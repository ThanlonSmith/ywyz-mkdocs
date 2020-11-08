![ubuntu logo](../imgs/ubuntu/202011061058.jpg)

#### 安装百度拼音输入法
<hr>

##### 1. 下载
官网下载DEB包：[https://srf.baidu.com/site/guanwang_linux/index.html](https://srf.baidu.com/site/guanwang_linux/index.html)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629015637957.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

解压缩下载好的zip文件：
```shell
thanlon@thanlon:~/下载$ unzip Ubuntu_Deepin-fcitx-baidupinyin-64.zip 
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629044601180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 2. 安装
更新和升级系统：
```shell
thanlon@thanlon:~$ sudo apt update && sudo apt upgrade -y
```
安装包管理工具 **`aptitude`**：
```shell
thanlon@thanlon:~$ sudo apt install aptitude
```
通过aptitude安装输入法依赖的fcitx和qt包：
```shell
thanlon@thanlon:~$ sudo aptitude install fcitx-bin fcitx-table fcitx-config-gtk fcitx-config-gtk2 fcitx-frontend-all
thanlon@thanlon:~$ sudo aptitude install qt5-default qtcreator qml-module-qtquick-controls2
```
安装输入法并解决可能出现的依赖：
```py
thanlon@thanlon:~/下载$ sudo dpkg -i fcitx-baidupinyin.deb
thanlon@thanlon:~/下载$ sudo apt install --fix-broken -y
```
<hr>

##### 3. 配置
找到设置中的【区域和语言】，选择【管理已安装的语言】：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020062904575418.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629025821554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

将键盘输入系统修改为 <kbd>fcitx键盘输入系统</kbd> 并应用到整个系统，然后重启系统：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629030144363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

系统重启后会让我们配置输入法，配置完成后就可以使用了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629050413429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200629050528153.png)

这个时候可以选择性把系统自带的IBus键盘输入法系统给删除：
```shell
thanlon@thanlon:~$ sudo apt purge ibus
```
<hr>

##### 4. 卸载
卸载输入法：
```py
thanlon@thanlon:~$ sudo dpkg --purge fcitx-baidupinyin:amd64
```
卸载不需要的依赖包：
```shell
# 或者使用:sudo apt autoremove
thanlon@thanlon:~$ sudo aptitude purge fcitx-bin fcitx-table fcitx-config-gtk fcitx-config-gtk2 fcitx-frontend-all
# 卸载qt相关包
thanlon@thanlon:~$ sudo aptitude purge qt5-default qtcreator qml-module-qtquick-controls2
```
<hr>

#### 安装MySQL8.0
<hr>

##### 1. 安装mysql
```python
$ sudo apt-get install mysql-server
```
<hr>

##### 2. 查看默认用户名和密码
mysql 安装完成后，默认用户名不是 root，为了方便，一般我们需要修改成我们想要的用户名和密码。进入配置文件：
```js
root@vivobook:/home/thanlon# vim /etc/mysql/debian.cnf
debian.cnf:
　　# Automatically generated for Debian scripts. DO NOT TOUCH!
　　[client]
　　host     = localhost
　　user     = debian-sys-maint
　　password = UwPyJArufIVRvuYC
　　socket   = /var/run/mysqld/mysqld.sock
　　[mysql_upgrade]
　　host     = localhost
　　user     = debian-sys-maint
　　password = UwPyJArufIVRvuYC
　　socket   = /var/run/mysqld/mysqld.sock
```
　　
可以看到，在配置文件中存在默认用户名和密码，在这里我们先使用配置文件中的用户名 user 和 password 登录到数据库。
<hr>

##### 3. 使用默认用户登录mysql
```python
$ mysql -udebian-sys-maint -p
```
<hr>

##### 4. 修改用户名和密码
查看 mysql 中默认数据库：**`show databases`**

使用数据库mysql：**`use mysql`**

将root用户的密码设置为空：**`update user set authentication_string='' where user='root'`**

>不可以使用**`update user set authentication_string=password(密码) where user='root'`**，其原因是该版本 mysql 没有 password 函数。

清空 root 用户默认的密码：`select user,authentication_string from user where user='root'`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200309164638560.png)

为 root 设置密码：**`ALTER user 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456'`**

查看设置 root 用户和密码：**`select user,authentication_string from user where user='root'`**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200309165116181.png)
<hr>

##### 5. 修改数据库的编码
可以登录mysql后使用`\s`查看默认的编码:
```python
Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	utf8
Conn.  characterset:	utf8
```
如果编码不是 utf8，我们需要把编码修改成 utf8，所以：
```python
# 修改配置文件
vim /etc/mysql/conf.d/mysql.cnf
# 加入下面两行
[mysqld]
character-set-server=utf8
```
再查看编码会发现都是 utf8 了:
```python
Server characterset:	utf8
Db     characterset:	utf8
Client characterset:	utf8
Conn.  characterset:	utf8
```
<hr>

#### 安装NAVIDIA显卡驱动程序
<hr>

##### 1. 使用附加驱动安装NAVIDIA驱动
在已安装的程序中找到并打开附加驱动：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051418093848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

你可以换成选项中其它三个 NVIDIA 驱动，我这里选择 nvidia-driver440 版本的，选中之后再选择应用更改等待自动下载安装就ok了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514181700919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

<hr>

##### 2. 使用命令安装NAVIDIA驱动
这里以安装 nvidia-driver-440 版本驱动为例，安装只需要下面一条命令就可以：
```shell
thanlon@thanlon:~$ sudo apt install nvidia-driver-440
```
安装完成后需要重新启动系统才能生效，重启系统后通过 **`nvidia-smi`** 命令可以查看有没有应用在使用NAVIDIA显卡驱动，如果存在这样的应用则表示安装成功：
```python
thanlon@thanlon:~$ nvidia-smi
Thu May 14 22:55:34 2020       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 440.64       Driver Version: 440.64       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce MX150       Off  | 00000000:02:00.0 Off |                  N/A |
| N/A   49C    P5    N/A /  N/A |    294MiB /  2002MiB |      7%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      1098      G   /usr/lib/xorg/Xorg                           103MiB |
|    0      1516      G   /usr/bin/gnome-shell                          95MiB |
|    0      2473      G   gnome-control-center                           1MiB |
|    0      2706      G   ...AAAAAAAAAAAACAAAAAAAAAA= --shared-files    92MiB |
+-----------------------------------------------------------------------------+
```
当然还可以通过系统设置里的系统信息来查看 NAVIDIA 显卡驱动安装是否成功：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020051422544632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

另外可以使用 **`nvidia-setting`** 命令打开NAVIDIA设置面板，也可以在应用程序中找到直接打开：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200514225747634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
<hr>

##### 3. 完全卸载NAVIDIA驱动
如果想卸载 NAVIDIA 驱动，使用附加驱动的方式只能切换驱动，但卸载不了驱动，只能通过命令的方式卸载：
```python
$ sudo apt-get --purge remove nvidia*
$ sudo apt-get --purge remove "*nvidia*"
$ sudo apt-get --purge remove "*cublas*" "cuda*"
$ sudo apt autoremove
```
<hr>

##### 4. 其它命令
```python
# 查看NAVIDIA的型号
$ lspci |grep -i nvidia
02:00.0 3D controller: NVIDIA Corporation GP108M [GeForce MX150] (rev a1)
# 查看NVIDIA驱动版本
sudo dpkg --list | grep nvidia-*
# 检查适合系统的NAVIDIA版本
$ nvidia-detector
nvidia-driver-440
```
<hr>

#### 下安装CUDA和cuDNN
<hr>

##### 1. 安装CUDA
CUDA：[**https://developer.nvidia.com/cuda-toolkit**](https://developer.nvidia.com/cuda-toolkit)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828145319154.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_left)
选择平台，不同的平台有不同安装步骤：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828150008310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_center)

根据下面的命令安装就可以了：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828150001298.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_center)
```python
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
$ sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
$ wget https://developer.download.nvidia.com/compute/cuda/11.0.3/local_installers/cuda-repo-ubuntu2004-11-0-local_11.0.3-450.51.06-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu2004-11-0-local_11.0.3-450.51.06-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-ubuntu2004-11-0-local/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get -y install cuda
```
CUDA 默认是被安装到 /usr/local/ 下，可以查看 CUDA 版本：
```python
$ cd /usr/local/cuda
$ cat version.txt 
CUDA Version 11.0.228
```
<hr>

##### 2. 安装cuDNN
cuDNN：[**https://developer.nvidia.com/cudnn**](https://developer.nvidia.com/cudnn)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828145648541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_left)

需要登录才能进入下载页面，还需要填写调查问卷！最后才进入下载页面 [**https://developer.nvidia.com/rdp/cudnn-download**](https://developer.nvidia.com/rdp/cudnn-download)：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828151940346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_left)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200828152856729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70#pic_left)

复制链接使用命令下载：
```python
$ wget https://developer.download.nvidia.cn/compute/machine-learning/cudnn/secure/8.0.2.39/11.0_20200724/cudnn-11.0-linux-x64-v8.0.2.39.tgz
```
解压缩：
```python
$ tar zxvf cudnn-11.0-linux-x64-v8.0.2.39.tgz
```
解压缩之后会得到 cuda 文件夹，接着执行下面的操作：
```python
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/
$ sudo chmod a+r /usr/local/cuda/include/cudnn.h
$ sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```
查看版本号：
```js
$ cat cuda/include/cudnn_version.h |grep ^#
#ifndef CUDNN_VERSION_H_
#define CUDNN_VERSION_H_
#define CUDNN_MAJOR 8
#define CUDNN_MINOR 0
#define CUDNN_PATCHLEVEL 2
#define CUDNN_VERSION (CUDNN_MAJOR * 1000 + CUDNN_MINOR * 100 + CUDNN_PATCHLEVEL)
#endif /* CUDNN_VERSION_H */
```
版本是 v8.0.2！
<hr>

#### 安装与使用TensorFlow
<hr>

##### 1. pip安装TensorFlow
这里使用的是当前日期最新版的 Ubuntu20.04 作为操作系统平台。20.04默认安装了Python3.8版本，但是没有安装pip，所以可以使用apt先安装pip：
```shell
$ sudo apt install python3-pip -y
```
查看 pip 的版本，如果不是最新版本顺便将 pip 升级到最新版本：
```shell
$ pip3  -V
$ pip3 install --upgrade pip
```
><font>安装 pip 的方式很多，例如可以把 pip 下载到本地使用 Python 命令安装等。安装 TensorFlow2 需要使用高于 19.0 的pip版本。</font>

安装之前需要把 pip 更新到最新版：
```shell
$ pip3 install --upgrade pip
```
安装 virtualenv 来帮助我们创建虚拟环境，这里使用的是国内阿里云的镜像源，下载速度会快很多：
```shell
$ pip3 install -U virtualenv -i https://mirrors.aliyun.com/pypi/simple
```
创建一个新的 Python 虚拟环境，创建一个 ./venv 目录来存放它：
```shell
$ virtualenv ./venv/
```
><font>如果我们想把系统中已有的库也加入到虚拟环境中，可以加上--system-site-packages。指定Python版本适用-p参数，如：-p python3</font>

激活该虚拟环境：
```shell
$ source venv/bin/activate
```
使用 pip 安装支持 GPU 的 TensorFlow 软件包，可以选择稳定版或预览版。安装稳定版的TensorFlow：
```shell
(venv) $ pip install tensorflow -i https://mirrors.aliyun.com/pypi/simple/
```
><font>如果安装只支持CPU版本：pip install tensorflow-cpu；同理安装只支持GPU版本：pip install tensorflow-cpu。指定版本：pip install tensorflow-gpu==2.2.0</font>

安装预览版使用下面的命令：
```shell
(venv) $ pip install tf-nightly -i https://mirrors.aliyun.com/pypi/simple/
```
安装之前一定要 <font>保证 /tmp 空闲足够充足，大概需要3G~4G</font>，当然也可以通过命令临时更改 /tmp 容量：
```shell
(venv) $ sudo mount -t tmpfs -o size=4G /tmp
(venv) $ df -h
/dev/sda10      4.0G     0  4.0G    0% /tmp
```
查看已安装的所有包：
```py
(venv) $ pip list
```
测试不指定 CPU 和 GPU 版本的 TensorFlow 是否不区分 CPU 和 GPU，是否会支持GPU。测试发现确实支持GPU：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200704034512148.png)

><font>直接安装的TensorFlow，并没有指定GPU版本，却得到GPU的支持。实际上新版本的TensorFlow不指定GPU版本同样支持GPU。对于1.15及更早版本，CPU和GPU软件包是分开的。pip install tensorflow==1.15是CPU版本，pip install tensorflow-gpu==1.15是GPU版本。</font>

<hr>

##### 2. Anaconda安装TensorFlow
Anaconda 是一个开源的 Python 发行版本，其中包含了很多流行的用于科学计算、数据分析的 Python 包。可以从官网下载，官网地址：**[https://www.anaconda.com/products/individual](https://www.anaconda.com/products/individual)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703134238628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

也可以到清华镜像站下载，官网链接：**[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703135637181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

我们这里直接从清华镜像站复制了 Anaconda 最新版本的链接，使用 wget 或者 axel 先下载到本地：
```shell
$ axel https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.1-Linux-x86_64.sh
```
执行脚本安装 Anaconda，安装过程中根据提示结合自己需要做例如选择安装路径、是否安装 Visual Stadio Code 的一些操作：
```shell
$ ./Anaconda3-5.3.1-Linux-x86_64.sh
```
Anaconda 安装成功之后接下来就可以安装 TensorFlow 了，Anaconda 默认使用国外的镜像源，在我们安装 TensorFlow 之前先更换为国内的镜像源，如更换为 <font>中科大镜像源</font>，第一个是最重要且主要的镜像源地址，第二个添不添加都可以：
```shell
$ conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main
$ conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free
```
更新conda：
```shell
thanlon@thanlon:~$ conda update -n base -c defaults conda
```
><font>conda比pip更加强大，可以安装非Python库。所以，非常推荐使用Anaconda安装TensorFlow。</font>

创建并进入虚拟环境，离开虚拟环境使用 **`conda deactivate`** 命令：
```shell
thanlon@thanlon:~$ conda create -n venv python=3.8
thanlon@thanlon:~$ conda activate venv
(venv) thanlon@thanlon:~$ conda deactivate
```
安装 TensorFlow GPU 最新版本，当前最新版是 tensorflow-gpu-2.2.0，已知版本的情况下可以指定版本：
```shell
(venv) thanlon@thanlon:~$ conda install tensorflow-gpu
```
为了检查安装的 TensorFlow 是否是 GPU 版本，需要安装 jupyter notebook 写程序测试：
```shell
(venv) thanlon@thanlon:~$ conda install jupyter notebook
(venv) thanlon@thanlon:~$ jupyter notebook
```
测试程序如下，说明我们已经成功安装支持 GPU 的TensorFlow：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200704032216938.png)

至此，Anaconda 安装 TensorFlow 已完成！
<hr>

##### 3. TensorFlow Docker容器的构建
首先安装Docker，Dockeer官方安装文档：**[https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703203416604.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

安装方式有三种，我这里选择存储库安装。卸载旧版本的Docker，如果之前安装过：
```py
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```
设置 Docker 存储库：
```shell
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```
添加 Docker 的官方 GPG 密钥：
```shell
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
设置稳定的存储：
```shell
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
更新 apt 程序包索引，并安装最新版本的 Docker Engine 和容器：
```shell
$ sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io
```
运行 hello-world 映像来验证是否正确安装了 Docker Engine：
```shell
$ sudo docker run -it hello-world
```
查看 Docker 是否启动：
```shell
$ systemctl status docker.service 
```
至此 Docker 安装成功，下面开始安装 TensorFlow。首先下载 TensorFlow Docker 镜像，TensorFlow 提供了很多 tensorFlow Docker 镜像。官方 TensorFlow Docker 映像位于 **[tensorflow/tensorflow](https://hub.docker.com/r/tensorflow/tensorflow/)** Docker Hub 代码库中：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703211731735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

>**References：[https://tensorflow.google.cn/install/docker#gpu_support](https://tensorflow.google.cn/install/docker#gpu_support)**

更换下载源为国内的镜像源 Docker 官方中国区：<kbd>https://registry.docker-cn.com</kbd>；阿里云：<kbd>https://pee6w651.mirror.aliyuncs.com</kbd>：
```shell
$ sudo vim /etc/docker/daemon.json
$ systemctl restart docker.service 
$ cat /etc/docker/daemon.json
{"registry-mirrors": ["https://pee6w651.mirror.aliyuncs.com"]}
```
这里就拉去一个支持 GPU 和 Jupyter 的版本：
```shell
$ docker pull tensorflow/tensorflow:latest-gpu-jupyter  # latest release w/ GPU support and Jupyter
```
成功安装：
```shell
$ sudo docker images
REPOSITORY              TAG                  IMAGE ID            CREATED             SIZE
tensorflow/tensorflow   latest-gpu-jupyter   8d78dd1e1b64        8 weeks ago         3.99GB
```
查看本地已有的容器：
```shell
$ sudo docker images
REPOSITORY              TAG                  IMAGE ID            CREATED             SIZE
tensorflow/tensorflow   latest-gpu-jupyter   8d78dd1e1b64        8 weeks ago         3.99GB
tensorflow/tensorflow   latest-gpu           f5ba7a196d56        8 weeks ago         3.84GB
```
接下来运行 TAG 是 latest-gpu 的容器，进入容器后可以查看内置的 Python 版本以及安装好的 TensorFlow：
```shell
$ sudo docker run -it tensorflow/tensorflow:latest-gpu
root@c4fceafad48c:/# python -V
Python 3.6.9
root@c4fceafad48c:/# pip list
...
tensorflow-gpu         2.2.0
...
```
可以另外开一个Terminal，查看正在运行的镜像：
```shell
$ sudo docker ps
CONTAINER ID        IMAGE                              COMMAND             CREATED             STATUS              PORTS               NAMES
49b31ca53c49        tensorflow/tensorflow:latest-gpu   "/bin/bash"         17 seconds ago      Up 16 seconds                           gallant_shirley
```
运行 TAG 是 latest-gpu-jupyter 的容器：
```shell
$ sudo docker run -p 8888:8888 tensorflow/tensorflow:latest-gpu-jupyter
```
浏览器访问下面的链接就可以使用 jupyter notebook：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200703225523709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070322554697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoYW5sb24=,size_16,color_FFFFFF,t_70)
