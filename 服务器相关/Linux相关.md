# Linux相关

## #虚拟机信息

虚拟机默认配置

root用户密码root

hadoop用户密码Raisei287517

虚拟机IP: 192.168.126.3

子网IP：192.168.126.2(虚拟机ifconfig中网卡en233的IPADD地址)

网关：192.168.126.1(w10控制面板VM8中IPV4的IP地址)

DNS1：119.29.29.29

宿主机IP:10.100.29.22

passwd user\_name修改用户密码

状态：

* IP配置完毕
* JDK安装，SSH安装完毕
* hadoop安装在/usr/local/hadoop
* jdk安装在/usr/lib/jvm/

## JDK内存分配参数

\-vmargs -Xms128M -Xmx512M -XX:PermSize=64M -XX:MaxPermSize=128M

```
-vmargs 说明后面是VM的参数，所以后面的其实都是JVM的参数了
-Xms128m JVM初始分配的堆内存
-Xmx512m JVM最大允许分配的堆内存，按需分配
-XX:PermSize=64M JVM初始分配的非堆内存
-XX:MaxPermSize=128M JVM最大允许分配的非堆内存，按需分配
```

## Linux文件 profile、bashrc、bash\_profile区别

* 首先读入全局环境变量设定档`**/etc/profile**`，然后根据其内容读取额外的设定的文档，如`**/etc/profile.d**`和`**/etc/inputrc**`；
* 根据不同使用者帐号，于其家目录内读取`**~/.bash_profile**`；
* 读取失败则会读取`**~/.bash_login**`；
* 再次失败则读取`**~/.profile**`（这三个文档设定基本上无差别，仅读取上有优先关系）；
* 最后，根据用户帐号读取`**~/.bashrc**`。

至于`**~/.profile**`与`**~/.bashrc**`都具有个性化定制功能，但`**~/.profile**`可以设定本用户专有的路径、环境变量等，它只能登入的时候执行一次。

`**~/.bashrc**`也是某用户专有设定文档，可以设定路径、命令别名，每次 shell script 的执行都会使用它一次。

#### 差异总结

`**/etc/profile**`，`**/etc/bashrc**` 是系统全局环境变量设定；

`**~/.profile**`，`**~/.bashrc**`用户家目录下的私有环境变量设定。

## Hadoop前提条件

VMware Pro16+Centos8

### 1、修改用户root权限

1、切换到root用户权限

Last login: Tue Sep 24 20:50:51 2013 from 192.168.30.171 \[user@Compile \~]$ su root password： \[root@Compile user]# 2、查看/etc/sudoers文件权限，如果只读权限，修改为可写权限

```shell
[root@Compile user]# ls -l /etc/sudoers
-r--r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
[root@Compile user]# chmod 777 /etc/sudoers
[root@Compile user]# ls -l /etc/sudoers
-rwxrwxrwx. 1 root root 4030 9月  25 00:57 /etc/sudoers
[root@Compile user]#
```

​ 3、执行vi命令，编辑/etc/sudoers文件，添加要提升权限的用户；在文件中找到root ALL=(ALL) ALL，在该行下添加提升权限的用户信息，如：

root ALL=(ALL) ALL user ALL=(ALL) ALL 说明：格式为（用户名 网络中的主机=（执行命令的目标用户） 执行的命令范围）

```
4、保存退出，并恢复/etc/sudoers的访问权限为440
```

\[root@Compile user]# chmod 440 /etc/sudoers \[root@Compile user]# ls -l /etc/sudoers -r--r-----. 1 root root 4030 9月 25 00:57 /etc/sudoers \[root@Compile user]# 5、切换到普通用户，测试用户权限提升功能

### 2、两种JDK安装方式

**一、yum安装jdk**

在linux上使用yum安装是非常粗暴无脑的，但仍然有需要注意的点，不然会掉坑里。这里说一下步骤。

1、检查是否已安装JDK及卸载 以下命令二选一，中括号选一即可 **yum list installed | grep \[java]\[jdk]** 你也可以用rpm -qa | grep \[java]\[jdk]\[gcj]

卸载JAVA环境 yum -y remove java-1.6.0-openjdk\* //表时卸载所有openjdk相关文件输入 yum -y remove tzdata-java.noarch //卸载tzdata-java

2、安装JDK

执行命令**yum -y list java**\*查看可安装java版本。查看JDK软件包列表 或者yum search java | grep -i --color jdk

选择版本安装,这里我们希望安装java1.8，因为我们的机器是64位的，所以选择安装java-1.8.0-openjdk-devel.x86\_64。

这里有个地方要注意，要选择-devel的安装，因为这个安装的是jdk，而那个不带-devel的安装完了其实是jre。

yum install -y java-1.8.0-openjdk-devel.x86\_64

或者如下命令，安装jdk1.8.0的所有文件(非常不建议这么做) yum install -y java-1.8.0-openjdk\*

**实际会自动安装非常多个java文件夹，这是正常的情况**

查看JDK是否安装成功 java -version

3、配置环境变量 yum安装JDK默认安装路径/usr/lib/jvm

在/etc/profile文件添加如下命令

```SH
#set java environment  
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
PATH=$PATH:$JAVA_HOME/bin  
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar  
export JAVA_HOME  CLASSPATH  PATH 
```

保存关闭profile文件，执行如下命令生效 source /etc/profile

4、设置好后来检验一些

```
1. `echo $JAVA_HOME # 检验变量值`
2. `java -version`
3. `$JAVA_HOME/bin/java -version # 与直接执行 java -version 一样`
```

**二、从官网下载包安装jdk**

如果你不喜欢yum安装的方式，想要使用官方提供的安装包进行传统方式的安装，可以使用如下步骤。

1.执行命令useradd java，新建用户java

2.执行命令passwd java，设置java用户密码

3.进入oracle官网，java8下载页面http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html。

大家也可以通过脚本之家的下载中选择相对应的版本

4.选择Accept License Agreement，点击jdk-8u131-linux-x64.tar.gz，获取到下载链接。注意，获取到的下载链接有时效（具体有效多久不清楚，反正隔天肯定不能用），请尽快复制到linux上进行下载。

5.进入到/home/java目录下，输入wget +地址，即可开始下载

6.等待下载成功

7.查看文件，发现下载后的文件名有奇怪的后缀，重命名下载文件

8.输入命令tar zxvf jdk-8u131-linux-x64.tar.gz解压安装包

9.输入命令vi /etc/profile，打开环境变量配置文件

在文件底部输入以下信息，并保存

```sh
JAVA_HOME=/usr/lib/jdk1.8.0_351
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export JRE_HOME
export PATH
export CLASSPATH
```

10.输入命令source /etc/profile，刷新环境变量配置文件使其立刻生效；输入java -version查看已安装的jdk版本

你要以为这就完成了，那就掉坑里了。虽然大部分时候这就够了，但还有一步操作最好做一下。

**建一个/usr/bin/java的java的超链接。**

```
ln -s /home/java/jdk1.8.0_131/bin/java /usr/bin/java
```

为什么要建这个超链接，因为一些自己注册的linux服务（如springboot的jar注册的服务），默认情况下从/usr/bin/java路径使用java，yum安装的时候，这个超链接会自动创建，如果你自己下载包安装的话，这个超链接就需要你手动创建了。

至此，从官网下载包安装jdk完成。

/usr/lib/python3.6/site-packages

### 3、全局环境变量配置文件

个人学习时可直接在/etc/profile配置JAVA\_HOME等变量,不必配置\~/profile

* **/etc/profile**:所有用户默认的全局环境变量，**优先修改这个！**
* **\~/profile**：在etc/profile的基础上定制覆盖
* **/etc/.bashrc**:找不到存在？
* **\~/.bashrc**：个体用户的bash局部变量，仅限与命令行使用？

### 4、Linux软件安装位置

* jdk通常安装位置\*\*/usr/lib/jvm/java-1.8.0\*\* /usr/java
* hadoop安装位置 **/usr/local/hadoop**
* **/etc**为全局配置文件存放处
* **/usr/lib**下一般放gcc，jdk，python的运行环境，sdk文件一般都在这

### 5、安装SSH、配置SSH无密码登陆

集群、单节点模式都需要用到 SSH 登陆（类似于远程登陆，你可以登录某台 Linux 主机，并且在上面运行命令），一般情况下，CentOS 默认已安装了 SSH client、SSH server，打开终端执行如下命令进行检验

检查是否安装了SSH:

​ `rpm -qa | grep ssh`

若需要安装，则可以通过 yum 进行安装（安装过程中会让你输入 \[y/N]，输入 y 即可）：

​ `sudo yum install openssh-clients`

​ `sudo yum install openssh-server`

接着执行如下命令测试一下 SSH 是否可用：

​ `ssh localhost`

此时会有如下提示(SSH首次登陆提示)，输入 yes 。然后按提示输入密码 hadoop，这样就登陆到本机了。

但这样登陆是需要每次输入密码的，我们需要配置成SSH无密码登陆比较方便。

首先输入 `exit` 退出刚才的 ssh，就回到了我们原先的终端窗口，然后利用 ssh-keygen 生成密钥，并将密钥加入到授权中：

1. `exit # 退出刚才的 ssh localhost`
2. `cd ~/.ssh/ # 若没有该目录，请先执行一次ssh localhost`
3. `ssh-keygen -t rsa # 会有提示，狂按回车就可以`
4. `cat id_rsa.pub >> authorized_keys # 加入授权`
5. `chmod 600 ./authorized_keys # 修改文件权限`

\~的含义

在 Linux 系统中，\~ 代表的是用户的主文件夹，即 “/home/用户名” 这个目录，如你的用户名为 hadoop，则 \~ 就代表 “/home/hadoop/”。 此外，命令中的 # 后面的文字是注释。

此时再用 `ssh localhost` 命令，无需输入密码就可以直接登陆了

**附录VMWare相关解决方案命令：**

开启ssh服务：`service sshd start/stop/restart`

SSH服务开机自动启动：chkconfig sshd on/off

开启服务后，检查服务状态：service sshd status

**修改/etc/ssh/ssh\_config配置文件，！不报错就不要改！**

```shell
Port 22 #运行端口
PermitRootLogin yes #允许远程连接
ListenAddress 0.0.0.0 #监听地址
ListenAddress ::
PasswordAuthentication yes #允许通过账号密码验证
```

开启 sshd 服务，输入 **sudo service sshd start**

![img](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20161008124204950)

检查 sshd 服务是否已经开启，输入**ps -e | grep sshd**

![img](https://img-blog.csdn.net/20161008124257998?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

或者输入**netstat -an | grep 22** 检查 **22** 号端口是否开启监听

![img](https://img-blog.csdn.net/20161008124408719?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

### 6、镜像源修改

1.安装Centos后默认的yum源如下 /etc/yum.repos.d/

```shell
[root@kangvcar ~]# ll /etc/yum.repos.d/
total 32
-rw-r--r--. 1 root root 1664 Dec  9  2015 CentOS-Base.repo
-rw-r--r--. 1 root root 1309 Dec  9  2015 CentOS-CR.repo
-rw-r--r--. 1 root root  649 Dec  9  2015 CentOS-Debuginfo.repo
-rw-r--r--. 1 root root  290 Dec  9  2015 CentOS-fasttrack.repo
-rw-r--r--. 1 root root  630 Dec  9  2015 CentOS-Media.repo
-rw-r--r--. 1 root root 1331 Dec  9  2015 CentOS-Sources.repo
-rw-r--r--. 1 root root 1952 Dec  9  2015 CentOS-Vault.repo2.把默认yum源备份(可选)
```

2.备份原来的yum源

```shell
[root@kangvcar ~]# mkdir /opt/centos-yum.bak
[root@kangvcar ~]# mv /etc/yum.repos.d/* /opt/centos-yum.bak/
```

3.下载aliyun yum源repo文件(对应自己的系统版本下载即可)

```shell
[root@kangvcar ~]# cat /etc/redhat-release        //查看系统的版本
CentOS Linux release 8.2.1511 (Core)
[root@kangvcar ~]# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
--2017-06-20 06:43:08--  http://mirrors.aliyun.com/repo/Centos-8.repo
Resolving mirrors.aliyun.com (mirrors.aliyun.com)... 112.124.140.210, 115.28.122.210
Connecting to mirrors.aliyun.com (mirrors.aliyun.com)|112.124.140.210|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2573 (2.5K) [application/octet-stream]
Saving to: ‘/etc/yum.repos.d/CentOS-Base.repo’
100%[=======================================================================================================>] 2,573       --.-K/s   in 0s      
2017-06-20 06:43:08 (118 MB/s) - ‘/etc/yum.repos.d/CentOS-Base.repo’ saved [2573/2573]
```

4.清除缓存

```shell
[root@kangvcar ~]# yum clean all
Loaded plugins: fastestmirror
Cleaning repos: base extras updates
Cleaning up everything
Cleaning up list of fastest mirrors
[root@kangvcar ~]# yum makecache        //把yum源缓存到本地，加快软件的搜索好安装速度
[root@kangvcar ~]# yum list        //总共列出了9954个包
```

## Linux基础命令

### 1、查看linux版本

一、查看Linux内核版本命令（两种方法）：

1、cat /proc/version

2、uname -a

二、查Linux系统版本的命令（3种方法）：

1、lsb\_release -a，即可列出所有版本信息：

这个命令适用于所有的Linux发行版，包括RedHat、SUSE、Debian…等发行版。

2、cat /etc/redhat-release，这种方法只适合Redhat系的Linux：

\[root@S-CentOS home]# cat /etc/redhat-release

CentOS release 6.5 (Final)

3、cat /etc/issue，此命令也适用于所有的Linux发行版。

### 2、yum操作

* 列出已安装软件 `yum list installed | grep []`
* 卸载`yum -y remove java-1.6.0-openjdk*` //表时卸载所有openjdk相关文件输入
* 查询\*\*`yum -y list java*`\*\*查看可安装java\*版本。
* 查询JDK软件包列表`yum search java | grep -i --color jdk`

### 3、文件读写

**文件的创建，查看，移动，删除**

ls -lh 查看信息

touch file 创建文件

echo 'chat' > file.py 写入

echo'chat '>> file.py 追加写入

cp file folder/

cp file filecopy

rmdir folder 删除空文件夹

rm -rf file 删除文件

**文件压缩解压**

tar -zxvf 压缩文件名.tar.gz 被压缩文件

tar -zxvf 压缩文件名.tar-gz

**rpm管理**

查看所有`rpm -qa`

安装命令：`rpm -ivh vsftpd.x86_64.0.3.0.2-22.el7.rpm`(注意现在应该在/mnt/Packages目录下，不然就要用rpm包的绝对路径)

**权限修改**

chmod 777 `filename`

### 4、硬盘拓展

新添加的硬盘无法自动识别需要 fdisk /dev/sdb然后新建分区，写入 结束后ls /dev/sd\* 便可以查看到

openwrt下的mkfs叫mke2fs

**linux下与存储相关的命令**

| free 查看内存使用情况                  |   |
| ------------------------------ | - |
| df 查看文件系统/挂载点的存储使用情况           |   |
| mkfs 对磁盘进行格式化                  |   |
| du 查看某个文件或者目录所占用的存储空间大小        |   |
| fdisk 查看系统所有的磁盘和磁盘分区；对物理磁盘进行分区 |   |

#### 格式化分区

`fdisk -l`

![image-20211015165225482](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20211015165225482.png)

`fdisk /dev/sdb`

![image-20211015165236454](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20211015165236454.png)

#### LVM简介

LVM全称Logical Volume Manager，逻辑卷管理，在linux系统下对磁盘分区进行管理的机制，是建立在物理磁盘上的抽象层。

物理卷（pv，physical volume） 可以理解为linux下的物理磁盘或者磁盘分区，如/dev/sdb（磁盘）、/dev/sdb1（磁盘分区，通过fdisk对/dev/sdb进行操作划分出来的磁盘分区，类似于windows下的C、D、E盘），但是和物理磁盘以及磁盘分区不同的是，物理卷含有和LVM相关的参数

卷组（vg，volume group） 由一个或者多个物理卷组成的卷组

逻辑卷（lv，logical volume） 通过卷组划分出来的分区，可以用来建立文件系统，例如/dev/mapper/centos-root 是根目录的文件系统挂载点，它就是建立在一个逻辑卷之上的，该文件系统的大小就是这个逻辑卷的大小

物理块（PE，physical extent） 每个物理卷被分为若干个物理块，是LVM寻址中的最小单元，有唯一编号，可以设置大小，默认为4MB

逻辑块（LE，logical extent） 每个逻辑卷被分为若干个逻辑卷，与PE是一一对应的

#### 案例：centos根目录扩容

```
[root@slave01 ~]# df
文件系统                  1K-块    已用    可用 已用% 挂载点
/dev/mapper/centos-root 6981632 6086596  895036   88% /
devtmpfs                1232124       0 1232124    0% /dev
tmpfs                   1242128       0 1242128    0% /dev/shm
tmpfs                   1242128   24092 1218036    2% /run
tmpfs                   1242128       0 1242128    0% /sys/fs/cgroup
/dev/sda1                508588  124772  383816   25% /boot
tmpfs                    248428       0  248428    0% /run/user/0
```

可以看到根目录只有12%的空间可用了，需要扩容一下

1.假设系统中有一块硬盘或者虚拟机的虚拟硬盘 /dev/sdb

2.通过上面 fdisk 部分的 fdisk /dev/sdb 操作来分出一块分区/dev/sdb1

```shell
[root@slave01 ~]# fdisk /dev/sd
sda   sda1  sda2  sdb   
[root@slave01 ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。

更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。

Device does not contain a recognized partition table
使用磁盘标识符 0x4472a336 创建新的 DOS 磁盘标签。

命令(输入 m 获取帮助)：n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
分区号 (1-4，默认 1)：1
起始 扇区 (2048-4194303，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-4194303，默认为 4194303)：+1G
分区 1 已设置为 Linux 类型，大小设为 1 GiB

命令(输入 m 获取帮助)：w
The partition table has been altered!

Calling ioctl() to re-read partition table.
正在同步磁盘。

[root@slave01 ~]# fdisk -l
磁盘 /dev/sdb：2147 MB, 2147483648 字节，4194304 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x4472a336

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
```

3.通过 pvcreate 命令创建物理卷

```
[root@slave01 ~]# pvcreate /dev/sdb1 
  Physical volume "/dev/sdb1" successfully created.
```

4.查看刚刚创建的物理卷/dev/sdb1

```
[root@slave01 ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               centos
  PV Size               7.51 GiB / not usable 3.00 MiB
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              1922
  Free PE               10
  Allocated PE          1912
  PV UUID               SFY7mk-Tkm3-pvih-tydL-zNaQ-3OcT-hv7hES

  "/dev/sdb1" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               MLLBUH-e9bY-2eMd-Lgpk-70px-4uan-nTRfHQ
```

5.vgdisplay 查看系统中的卷组，看到Free PE（空闲物理块）只有10块

```
[root@slave01 ~]# vgdisplay 
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <7.51 GiB
  PE Size               4.00 MiB
  Total PE              1922
  Alloc PE / Size       1912 / <7.47 GiB
  Free  PE / Size       10 / 40.00 MiB
  VG UUID               O40ce2-OEPo-N8l1-tuu7-vmf8-kDHE-mHQdY9
```

6.通过 vgextend 将物理卷sdb1加入卷组centos

```
[root@slave01 ~]# vgextend centos /dev/sdb1
  Volume group "centos" successfully extended
```

7.再次查看卷组，可以看到Free PE（空闲物理块）已经是265块

```
[root@slave01 ~]# vgdisplay 
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               8.50 GiB
  PE Size               4.00 MiB
  Total PE              2177
  Alloc PE / Size       1912 / <7.47 GiB
  Free  PE / Size       265 / <1.04 GiB
  VG UUID               O40ce2-OEPo-N8l1-tuu7-vmf8-kDHE-mHQdY9
```

8.通过 lvextend 命令扩展根目录挂载点 /dev/mapper/centos-root

```
[root@slave01 ~]# lvextend -L +1G /dev/mapper/centos-root 
  Size of logical volume centos/root changed from <6.67 GiB (1707 extents) to <7.67 GiB (1963 extents).
  Logical volume centos/root successfully resized.

```

到此只是对逻辑卷进行了扩容，通过df还看不到根目录的空间扩充，还要对文件系统进行扩容才能完成

9.通过 resize2fs 或者 xfs\_growfs 命令对文件系统进行扩容

```
[root@slave01 ~]# resize2fs /dev/mapper/centos-root 
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block 当尝试打开 /dev/mapper/centos-root 时
找不到有效的文件系统超级块.
```

这里有时候使用 resize2fs 会出错，查看一下根目录的文件系统

查看\*/dev/mapper/centos-root\*文件系统

```
[root@slave01 ~]# cat /etc/fstab | grep centos-root
/dev/mapper/centos-root /                       xfs     defaults        0 0
10.看到文件系统xfs，使用 xfs_growfs 命令

[root@slave01 ~]# xfs_growfs /dev/mapper/centos-root 
meta-data=/dev/mapper/centos-root isize=256    agcount=4, agsize=436992 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=1747968, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 1747968 to 2010112
```

11.df 查看扩容后的各个目录和挂载点大小

```
[root@slave01 ~]# df
文件系统                  1K-块    已用    可用 已用% 挂载点
/dev/mapper/centos-root 8030208 6090720 1939488   76% /
devtmpfs                1232124       0 1232124    0% /dev
tmpfs                   1242128       0 1242128    0% /dev/shm
tmpfs                   1242128   24096 1218032    2% /run
tmpfs                   1242128       0 1242128    0% /sys/fs/cgroup
/dev/sda1                508588  124772  383816   25% /boot
tmpfs                    248428       0  248428    0% /run/user/0
```

可以看到现在现在根目录 / 现在有24%的可用空间了

### 5、前后台任务切换

前台运行：

```
$ bash run.sh           
hello world
hello world
hello world
hello world
```

后台运行：

```
$ nohup bash  run.sh 2>&1 &                                                                     
[1] 31520
nohup: ignoring input and appending output to 'nohup.out'

$ tailf nohup.out 
hello world
hello world
hello world
hello world
hello world
hello world
```

其中`31520`表示作业id。如果不加nohup, 当终端退出时，run.sh对应的进程会收到SIGHUP信号，而进程对SIGHUP信号的默认处理方式是终止，这显然不是我们想要的。而加上nohup之后，run.sh对应的进程会忽略SIGHUP信号，即终端退出并不影响进程继续运行.

#### 前台运行转后台

很多情况下，我们并不知晓某个任务会运行多久，因此一般在前台运行该任务。如果发现该任务一时半会完成不了，能不能将它从前台运行转到后台？答案是可以，举例说明如何操作：

首先在前台运行run.sh

```
$ bash run.sh 
hello world
hello world
hello world
```

输入ctrl + z 将该前台任务挂起

```
^Z
[1]  + 32295 suspended  bash run.sh
```

运行jobs命令，查看任务号(可以看到run.sh对应的任务号是1)

```
$ jobs                                                                                                  
[1]  + suspended  bash run.sh
```

运行bg命令，将任务从前台转到后台

```
bg %1
```

#### 后台运行转前台

既然任务可以从前台转后台，那反过来从后台转前台是否可行呢？答案是可以的 首先以后台运行run.sh

```
$ nohup bash  run.sh 2>&1 & 
[1] 482
nohup: ignoring input and appending output to 'nohup.out'
```

查看后台任务

```
$ jobs
[1]  + running    nohup bash run.sh 2>&1
```

运行fg命令，将任务从后台转到前台

```
$ fg %1                    
[1]  + 482 running    nohup bash run.sh 2>&1
```

## 奇奇怪怪的BUG和tips

### ‌Termux小程序‌you-get

安装you-get

第一步：更新安装器 pkg update && pkg upgrade

第二步：获取sd卡读写权限 termux-setup-storage

第三步：安装python和ffmpeg pkg install python ffmpeg

第四步：安装you-get pip install you-get

### apt-get: command not found

**1、第一种可能：**

nfs客户端段显示命令不存在。主要是因为系统为最小化安装，一些命令没有安装导致，使用下面命令安装即可。

yum install nfs-utils.x86\_64

**2、第二种原因：**

包管理器不是apt-get，只能是yum

### 查找linux软件安装文件夹

例如想知道redis安装在哪了, redis的服务端是"redis-server" 实验环境centos 7.6

方法一: locate

1.安装mlocate

yum install mlocate

2.更新数据库

updatedb

3.查找

locate redis-server

/usr/local/bin/ 就是redis的安装位置

方法二: whereis 只显示可执行文件 whereis redis-server

方法三: which只显示文件 which redis-server

方法四: find 文件和文件夹 find / -name redis-server

### Linux系统分类

**一般来说linux系统基本上分两大类：**

RedHat系列：Redhat、Centos、Fedora等 Debian系列：Debian、Ubuntu等

**RedHat 系列**

* 常见的安装包格式 rpm包,安装rpm包的命令是“rpm -参数”
* 包管理工具 yum
* 支持tar包

**Debian系列**

* 常见的安装包格式 deb包,安装deb包的命令是“dpkg -参数”
* 包管理工具 apt-get
* 支持tar包

_tar 只是一种压缩文件格式，它只是把文件压缩打包而已。 rpm 相当于windows中的安装文件，它会自动处理软件包之间的依赖关系。 优缺点来说，rpm一般都是预先编译好的文件，它可能已经绑定到某种CPU或者发行版上面了。 tar一般包括编译脚本，你可以在你的环境下编译，所以具有通用性。 如果你的包不想开放源代码，你可以制作成rpm，如果开源，用tar更方便了。 tar一般都是源码打包的软件，需要自己解包，然后进行安装三部曲，./configure, make, make install. 来安装软件。 安装rpm包的命令是“rpm -参数”，安装deb包的命令是“dpkg -参数”。而linux系统很方便和人性化的一点就是很多软件或服务根本就不用我们去下载，直接使用相应的命令就可以管理了，可能这就是传说中的 “云”的概念。_

### wget命令行下载

下载hadoop

wget https://mirrors.cnnic.cn/apache/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz

### curl命令

```bash
-A/--user-agent <string>              设置用户代理发送给服务器
-b/--cookie <name=string/file>    cookie字符串或文件读取位置
-c/--cookie-jar <file>                    操作结束后把cookie写入到这个文件中
-C/--continue-at <offset>            断点续转
-D/--dump-header <file>              把header信息写入到该文件中
-e/--referer                                  来源网址
-f/--fail                                          连接失败时不显示http错误
-o/--output                                  把输出写到该文件中
-O/--remote-name                      把输出写到该文件中，保留远程文件的文件名
-r/--range <range>                      检索来自HTTP/1.1或FTP服务器字节范围
-s/--silent                                    静音模式。不输出任何东西
-T/--upload-file <file>                  上传文件
-u/--user <user[:password]>      设置服务器的用户和密码
-w/--write-out [format]                什么输出完成后
-x/--proxy <host[:port]>              在给定的端口上使用HTTP代理
-#/--progress-bar                        进度条显示当前的传送状态
```

```bash
bash <(curl -s -L https://git.io/v2ray-setup.sh --progress)
curl -L -o /root/xray.sh https://cdn.jsdelivr.net/gh/Misaka-blog/Xray-script@master/xray.sh
```

#### 文件下载

* curl命令可以用来执行下载、发送各种HTTP请求，指定HTTP头部等操作
* curl是将下载文件输出到stdout，将进度信息输出到stderr，不显示进度信息使用--silent选项。

`--progress`显示进度条

```
curl [网址]
```

#### 断点续传

```shell
> curl -O -u 'rumenz':'test' ftp://rumenz.com/jdk.tar.gz
```

> 然后你的连接突然断开，你可以用以下命令继续下载

```shell
> curl -C -  -O  -u 'rumenz':'test' ftp://rumenz.com/jdk.tar.gz
```

> 注意断点续传的参数是`-C`, 要自动续传的话要使用 `-C -`, 否则需要手工指定断点的字节位置.

### curl指定目录

我知道我可以使用以下2条命令来下载文件：

```bash
curl -O example.com/file.zip
wget example.com/file.zip
```

但我希望他们进入特定目录。 因此，我可以执行以下操作：

```bash
curl -o mydir/file.zip example.com/file.zip
wget -O mydir/file.zip example.com/file.zip
```

有没有一种方法不必指定文件名？ 像这样：

```bash
curl -dir mydir example.com/file.zip
```

4个解决方案

51 votes

下一行会将所有文件下载到您提到的目录中。

```
wget -P /home/test www.xyz.com
```

此处文件将下载到`/home/test`目录

[Kishan](https://stackoverflow.com/users/2076606) answered 2020-07-07T23:45:34Z

15 votes

我知道这是一个老问题，但是可以随心所欲地做你想做的事情

```
rm directory/somefile.zip
rmdir directory
mkdir directory
curl --http1.1 http://example.com/somefile.zip --output directory/somefile.zip
```

首先，如果此脚本已编写脚本，则需要确保已删除文件（如果已存在），然后删除目录，然后进行curl下载，否则curl将失败，并显示文件和目录已存在错误。

[mewasthere](https://stackoverflow.com/users/6486709/mewasthere) answered 2020-07-07T23:45:59Z

4 votes

最简单的方法是将`wgetinside somedir http://example.com/file.zip`放入子壳中

```
  (cd somedir; wget example.com/file.zip)
```

您可以使该shell函数起作用（例如在您的`wgetinside somedir http://example.com/file.zip`中）

```
  wgetinside() {
    ( cd $1 ; shift; wget $* )
  }
```

然后输入`wgetinside somedir http://example.com/file.zip`

[Basile Starynkevitch](https://stackoverflow.com/users/841108/basile-starynkevitch) answered 2020-07-07T23:46:27Z

1 votes

简短的回答是不，因为curl和wget自动写入STDOUT。 它没有内置用于将下载文件放置到目录中的选项。

```
-o/--output <file> Write output to <file> instead of stdout (Curl)

-O,  --output-document=FILE    write documents to FILE. (WGet)
```

### 指定环境alternatives命令详解

[指定环境alternatives命令详解](https://www.cnblogs.com/lpfuture/p/4638425.html)

alternatives是Linux下的一个功能强大的命令。只能在root权限下执行。如系统中有几个命令功能十分类似，却又不能随意删除，那么可以用 alternatives 来指定一个全局的设置。

alternatives常用于同一个系统中安装同一软件的多个版本。比如为了开发需要，我需要安装JDK1.4.2，同时还需要JDK1.6.10，我怎么样才能忽略安装路径，按照我自己的意思，使用我想要的java版本呢？

alternatives的命令行执行如下：

```shell
[root@localhost tools]# alternatives 
alternatives version 1.3.36 - Copyright (C) 2001 Red Hat, Inc.
This may be freely redistributed under the terms of the GNU Public License.

usage: alternatives --install <link> <name> <path> <priority>
          [--initscript <service>]
          [--slave <link> <name> <path>]*
    alternatives --remove <name> <path>
    alternatives --auto <name>
    alternatives --config <name>
    alternatives --display <name>
    alternatives --set <name> <path>

common options: --verbose --test --help --usage --version
        --altdir <directory> --admindir <directory>
```

说明： alternatives --install 其中， install表示安装 link是符号链接 name则是标识符 path是执行文件的路径 priority则表示优先级

例如：

```shell
alternatives --config java
```

问题现象

### ###HADOOP无法通过ip访问服务页面

**问题1：hdfs-site.xml配置项** 通过jps命令查看java进程的状态，HADOOP相关的进程运行正常。 jps是jdk提供的一个查看当前java进程的小工具， 可以看做是JavaVirtual Machine Process Status Tool的缩写。

```
[root@node4 ~]# jps
25059 SecondaryNameNode
25347 ResourceManager
25556 NodeManager
24805 DataNode
29269 Jps
24633 NameNode
```

通过命令`netstat -ntlp`查看网络端口服务情况，发现local address列给出的ip地址除了127.0.0.1就是0.0.0.0，这些本地有效的地址，是无法对外提供服务的，这才是问题的关键。

```shell
[root@node4 ~]# netstat -ntlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:43759         0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 0.0.0.0:50070        0.0.0.0:*               LISTEN      24633/java          
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      12782/sshd          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      2325/master         
tcp        0      0 0.0.0.0:50010           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 0.0.0.0:50075           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 0.0.0.0:50020           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      24633/java          
tcp        0      0 0.0.0.0:50090           0.0.0.0:*               LISTEN      25059/java          
tcp6       0      0 :::22                   :::*                    LISTEN      12782/sshd          
tcp6       0      0 127.0.0.1:8088          :::*                    LISTEN      25347/java          
tcp6       0      0 ::1:25                  :::*                    LISTEN      2325/master         
tcp6       0      0 :::13562                :::*                    LISTEN      25556/java          
tcp6       0      0 :::43451                :::*                    LISTEN      25556/java          
tcp6       0      0 127.0.0.1:8030          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8031          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8032          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8033          :::*                    LISTEN      25347/java          
tcp6       0      0 :::8040                 :::*                    LISTEN      25556/java          
tcp6       0      0 :::8042                 :::*                    LISTEN      25556/java          
```

修改HADOOP\_HOME/etc/hadoop/hdfs-site.xml文件，加入

```xml
<property>
        <name>dfs.namenode.http-address</name>
        <value>node4:50070</value>
</property>
```

再次用netstat -ntlp查看

```shell
[root@node4 ~]# netstat -ntlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:43759         0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 10.60.8.28.50070        0.0.0.0:*               LISTEN      24633/java          
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      12782/sshd          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      2325/master         
tcp        0      0 0.0.0.0:50010           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 0.0.0.0:50075           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 0.0.0.0:50020           0.0.0.0:*               LISTEN      24805/java          
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      24633/java          
tcp        0      0 0.0.0.0:50090           0.0.0.0:*               LISTEN      25059/java          
tcp6       0      0 :::22                   :::*                    LISTEN      12782/sshd          
tcp6       0      0 127.0.0.1:8088          :::*                    LISTEN      25347/java          
tcp6       0      0 ::1:25                  :::*                    LISTEN      2325/master         
tcp6       0      0 :::13562                :::*                    LISTEN      25556/java          
tcp6       0      0 :::43451                :::*                    LISTEN      25556/java          
tcp6       0      0 127.0.0.1:8030          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8031          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8032          :::*                    LISTEN      25347/java          
tcp6       0      0 127.0.0.1:8033          :::*                    LISTEN      25347/java          
tcp6       0      0 :::8040                 :::*                    LISTEN      25556/java          
tcp6       0      0 :::8042                 :::*                    LISTEN      25556/java          

```

**问题2：selinux** 按照道理应该可以访问50070端口了，但是仍然不行。再检查selinux，发现状态是enabled。

* 查看SELINUX的状态

```shell
[root@node4 ~]# /usr/sbin/sestatus -v
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28

Process contexts:
Current context:                unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
Init context:                   system_u:system_r:init_t:s0
/usr/sbin/sshd                  system_u:system_r:sshd_t:s0-s0:c0.c1023

File contexts:
Controlling terminal:           unconfined_u:object_r:user_devpts_t:s0
/etc/passwd                     system_u:object_r:passwd_file_t:s0
/etc/shadow                     system_u:object_r:shadow_t:s0
/bin/bash                       system_u:object_r:shell_exec_t:s0
/bin/login                      system_u:object_r:login_exec_t:s0
/bin/sh                         system_u:object_r:bin_t:s0 -> system_u:object_r:shell_exec_t:s0
/sbin/agetty                    system_u:object_r:getty_exec_t:s0
/sbin/init                      system_u:object_r:bin_t:s0 -> system_u:object_r:init_exec_t:s0
/usr/sbin/sshd                  system_u:object_r:sshd_exec_t:s0
```

编辑/etc/selinux/config文件SELINUX=enforcing修改成SELINUX=disable，重启服务器。再试。修改后的selinux

```shell
[root@node4 ~]# /usr/sbin/sestatus -v
SELinux status:                 disabled
```

**问题3：firewall（iptables端口开放）** 关闭selinux之后，仍然无法访问页面，再查看iptables防火墙的设置。

```shell
[root@node4 sbin]# firewall-cmd --state
running
[root@node4 sbin]# firewall-cmd --get-service
RH-Satellite-6 amanda-client amanda-k5-client bacula bacula-client bitcoin bitcoin-rpc bitcoin-testnet bitcoin-testnet-rpc ceph ceph-mon cfengine condor-collector ctdb dhcp dhcpv6 dhcpv6-client dns docker-registry dropbox-lansync elasticsearch freeipa-ldap freeipa-ldaps freeipa-replication freeipa-trust ftp ganglia-client ganglia-master high-availability http https imap imaps ipp ipp-client ipsec iscsi-target kadmin kerberos kibana klogin kpasswd kshell ldap ldaps libvirt libvirt-tls managesieve mdns mosh mountd ms-wbt mssql mysql nfs nrpe ntp openvpn ovirt-imageio ovirt-storageconsole ovirt-vmconsole pmcd pmproxy pmwebapi pmwebapis pop3 pop3s postgresql privoxy proxy-dhcp ptp pulseaudio puppetmaster quassel radius rpc-bind rsh rsyncd samba samba-client sane sip sips smtp smtp-submission smtps snmp snmptrap spideroak-lansync squid ssh synergy syslog syslog-tls telnet tftp tftp-client tinc tor-socks transmission-client vdsm vnc-server wbem-https xmpp-bosh xmpp-client xmpp-local xmpp-server
```

增加50070端口到允许，重启防火墙服务。

```shell
[root@node4 sbin]# firewall-cmd --zone=public --add-port=50070/tcp --permanent
success
[root@node4 sbin]# firewall-cmd --reload
success
```

处理结果 页面可以访问了

### 制作bin安装文件

linux 下制作二进制 .bin 的文件 孤雁奉献 制做方法是使用cat 命令将执行脚本和打包文件同事放到一个.bin的文件里 这样安装的时候只要使用一个包，直接执行该包即可安装完毕，简单方便。

例：制作安装apache、mysql的安装脚本包

1.将源码包先打包 # tar zcvf packages.tar.gz httpd-2.0.63.tar.bz2 mysql-5.0.33.tar.gz

2.编写脚本如下：

```shell
# cat install.sh 
#!/bin/bash 
dir_tmp=/root/installapache 
mkdir $dir_tmp sed -n -e '1,/^exit 0$/!p' $0 > "${dir_tmp}/packages.tar.gz" 
2>/dev/null 
cd $dir_tmp 
tar zxf packages.tar.gz tar jxf httpd-2.0.63.tar.bz2 
cd httpd-2.0.63 
./configure --prefix=/tmp/apache2 
make make install 
cd $dir_tmp tar zxf mysql-5.0.33.tar.gz 
cd mysql-5.0.33 ./configure --with-charset=gbk --with-extra-charsets=binary,latin1,gb2312 --localstatedir=/home/db --with-mysqld-ldflags=-all-static -enable-assembler --with-innodb --prefix=/tmp/mysql5 
make make install exit 0   
#cat install.sh packages.tar.gz >install.bin  
```

这样就生成install.bin的安装文件，改文件是由shell脚本和二进制合成的。前半部分是脚本后半部分是二进制文件，用strings等二进制查看命令可以看到 最主要的是下面这句，是将二进制文件从.bin文件里分离出来 sed -n -e '1,/^exit 0$/!p' $0 > "${dir\_tmp}/packages.tar.gz" 2>/dev/null 安装的时候直接执行 sh install.bin 安装这个方法可以将我们平时常使用的安装脚本化，然后打包。以后使用就方便了。

### hadoop启动报错

**node02: Warning: Permanently added ‘node02,192.168.139.129‘ (ECDSA) to the list of known hosts.**

多半是yarn-site.xml文件有东西配错了

第二种可能：

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20210406135053723.png) 删除know\_hosts文件，删除.ssh文件夹 重新生成.ssh，并且重新启动hadoop

### DFSRouter: RECEIVED SIGNAL 15: SIGTERM

ERROR org.apache.hadoop.hdfs.server.namenode.NameNode: RECEIVED SIGNAL 15: SIGTERM

二、分析

1、signal 15含意是使用不带参数的kill命令时终止进程,初步判断，由于文件数据块的原因造成datanode失联，手动执行如下命令

hadoop dfsadmin -refreshNodes

三、解决方法

手动执行hadoop dfsadmin -refreshNodes

问题总结：balancer停了一段时间了，以前balancer的 pid现在是router里的一个线程，所以stop balancer的时候，router退出了。

### 拒绝连接

java.io.IOException: java.net.ConnectException: Call From node01/192.168.100.10 to node01:10020 failed on connection exception: java.net.ConnectException: Connection refused; For more details see: http://wiki.apache.org/hadoop/ConnectionRefused

报错信息提示，在访问端口 100020的时候出错，这表示DataNode 需要访问 MapReduce JobHistory Server，而默认值是： 0.0.0.0:10020 。

jps 查看 ， 麻蛋 ，忘了打开，

```XML
<property>
    <name>mapreduce.jobhistory.address</name>
    <!-- 配置实际的主机名和端口-->
    <value>{namenode} :10020</value>
</property>
```

`mr-jobhistory-daemon.sh start historyserver`

**在执行一个Hadoop的文件读写任务是发生这个错误：Call From master/192.168.170.128 to master:8020 failed on connection exception: java.net.ConnectException: Connection。**

意思大致是无法连接到master的8020端口。该问题造成的主要原因是和Hadoop以及Linux系统的版本有关，dfs.defaultFS的默认端口号为8020，但一般 在配置core-site.xml时大家都会配成9000（具体为什么我也不知道，网上只教怎么配，没说为什么），但是在Ubuntu系统中9000端口好像不起作用。将其重新改回8020即可。

### tar打包

功能描述：打包与解打包命令：tar

选项：

\-c：打包；

\-x：解打包；

\-f：指定压缩包的文件名。压缩包的扩展名是用来给管理员识别格式的，

\-v：显示打包文件过程；

\-C 目录：指定解打包位置；

\-z：压缩和解压缩 ".tar.gz"格式

\-P ：可以使用绝对路径来压缩

tar zcfv Desktop.tar.gz Desktop/ 打包

tar zxfv hadoop.tar.gz -C /usr/local 解压

## #Centos8集群

### 1、基本配置

> **输入法配置**

使用指令： yum install ibus-libpinyin 安装

​ reboot 安装好后重启

设置==>语言，添加

> **网络配置**

`netstat -ntlp`查看网络端口

**注意！！控制面板VM适配器的IP地址，VM网络编辑器子网网关IP，虚拟机ifconfig的IP，全部不能相同，但必需在同一网段下**

1.使用dhclient生成IP地址，ifconfig查看，复制IP

**控制面板VM适配器的IP地址：192.168.126.1**

**VM网络编辑器子网网关IP：192.168.126.2**

**虚拟机IP地址：192.168.126.3**

2.编辑 sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33

**删除两行：UUID和HWADDR物理地址**

**修改IPADDR=IP地址**

```shell
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static #静态IP
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
# UUID=92cb2a30-bb63-410b-8881-2c1b2da6ba22 #屏蔽物理地址
DEVICE=ens33
ONBOOT=yes #开机网卡自启动
IPADDR=192.168.126.3 #虚拟机IP地址，与前两个都不同
NETMASK=255.255.255.0 #子网掩码
GATEWAY=192.168.126.2 #网关应该与VM网络编辑器子网网关IP一致
DNS1=119.29.29.29
DNS2=8.8.8.8

```

3.重启网卡systemctl restart network.service

如果报错Failed to restart network.service: Unit network.service not found.

则使用nmcli c up ens33

测试：ifconfig查看IP地址，虚拟机ping宿主机10.100.29.22，宿主机ping虚拟机验证网络连通

### 2、虚拟机网络连接不成功

注意！！控制面板VM适配器的IP地址，VM网络编辑器子网网关IP，虚拟机ifconfig的IP，全部不能相同，但必需在同一网段下

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20181025170058654) 接下来配置虚拟机： ![在这里插入图片描述](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20181025190146298.png)

\*\*注意！！控制面板VM适配器中的IP地址和VM虚拟网络编辑器网关IP是一致的!!实际虚拟机IP地址从外面看为2

所有网关都设置为同一个地址192.168.x.2 \*\*

### 3、修改主机名(永久)

```
此处本系统是redhat7.1, 修改本地主机名的文件是: /etc/hostname（版本不同，可能不同）
```

​ 把里面的内容（原主机名）改为目标主机名即可

​ 修改以后， 立即生效， 不用重启

### 4、防火墙

firewalld 方式 启动： systemctl start firewalld 查看状态： systemctl status firewalld 禁用，禁止开机启动： systemctl disable firewalld 停止运行： systemctl stop firewalld 重启：firewall-cmd --reload

**遇到Unit iptables.service could not be found**：

1、安装iptables

```shell
yum install iptables-services
```

2、 启动iptables

```shell
systemctl enable iptables
systemctl start iptables
```

3、防火墙状态：

```shell
#实际可能不是如下命令
service iptables status
关闭防火墙:
service iptables stop
打开防火墙:
service iptables start 
```

### 5、SSH配置

`netstat -ntlp`查看网络端口

1、 首先，要确保CentOS安装了 **openssh-server**，在终端中输入 **yum list installed | grep openssh-server**

![image-20210925000542689](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20210925000542689.png)

此处显示已经安装了 openssh-server，**如果又没任何输出显示表示没有安装** openssh-server，

通过输入 **`yum install openssh-server`来进行安装openssh-server**

2、 找到了 **/etc/ssh/** 目录下的sshd服务配置文件 **sshd\_config**，用Vim编辑器打开

将文件中，关于监听端口、监听地址前的 # 号去除

```shell
Port 22 #运行端口
PermitRootLogin yes #允许远程连接
ListenAddress 0.0.0.0 #监听地址
ListenAddress ::
PasswordAuthentication yes #允许通过账号密码验证
```

3、 开启 sshd 服务，输入 **`sudo service sshd start`**

检查 sshd 服务是否已经开启，输入\*\*`ps -e | grep sshd`\*\*

![image-20210925000823111](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20210925000823111.png)

或者输入\*\*`netstat -an | grep 22`\*\* 检查 **22** 号端口是否开启监听

![image-20210925000831948](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20210925000831948.png)

### 集群搭建

**前提条件：基本配置，主机名，防火墙，SSH配置，JDK配置，hadoop安装配置，环境变量配置，镜像源修改**

1、在master的/etc/hosts文件中添加内容

```shell
192.168.126.3 master
192.168.126.4 slave1
192.168.126.5 slave2
```

2、克隆slave虚拟机

3、配置slave虚拟机的IP地址，网关相同

## phpStudy

使用 SSH 连接工具 连接到您的 Linux服务器后，根据系统执行相应命令开始安装（大约2分钟完成面板安装）：

Centos安装脚本&#x20;

`yum install -y wget wget -O install.sh https://download.xp.cn/install.sh`\
`sh install.sh`

#### 安装后自动运行

```
docker daemon running 
centos_env container running 
webpanel running 
phpstudy running 

请用浏览器访问面板:
外网:http://117.178.143.123:9080/A19480
内网:http://192.168.1.10:9080/A19480
系统初始账号:admin
系统初始密码:wxp6BRU5mH
官网:https://www.xp.cn
如果使用的是云服务器，请至安全组开放9080端口
如果使用ftp，请开放21以及30000-30050端口
```

![image-20221228225639814](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20221228225639814.png)

![image-20221228225656546](https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20221228225656546.png)

IP地址

小皮面板http://192.168.1.10:9080/A19480

博客网站http://192.168.1.10/wordpress

数据库http://192.168.1.10/phpMyAdmin\_4.9.0.1/
