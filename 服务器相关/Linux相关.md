# 虚拟机信息


虚拟机默认配置

root用户密码root

hadoop用户密码Raisei287517

虚拟机IP: 192.168.126.3 

子网IP：192.168.126.2(虚拟机ifconfig中网卡en233的IPADD地址)

网关：192.168.126.1(w10控制面板VM8中IPV4的IP地址)

DNS1：119.29.29.29

宿主机IP:10.100.29.22

passwd user_name修改用户密码

状态：

- IP配置完毕
- JDK安装，SSH安装完毕
- hadoop安装在/usr/local/hadoop
- jdk安装在/usr/lib/jvm/


# JDK内存分配参数

-vmargs -Xms128M -Xmx512M -XX:PermSize=64M -XX:MaxPermSize=128M

	-vmargs 说明后面是VM的参数，所以后面的其实都是JVM的参数了
	-Xms128m JVM初始分配的堆内存
	-Xmx512m JVM最大允许分配的堆内存，按需分配
	-XX:PermSize=64M JVM初始分配的非堆内存
	-XX:MaxPermSize=128M JVM最大允许分配的非堆内存，按需分配

# Linux文件 profile、bashrc、bash_profile区别


-   首先读入全局环境变量设定档`**/etc/profile**`，然后根据其内容读取额外的设定的文档，如`**/etc/profile.d**`和`**/etc/inputrc**`；
-   根据不同使用者帐号，于其家目录内读取`**~/.bash_profile**`；
-   读取失败则会读取`**~/.bash_login**`；
-   再次失败则读取`**~/.profile**`（这三个文档设定基本上无差别，仅读取上有优先关系）；
-   最后，根据用户帐号读取`**~/.bashrc**`。

至于`**~/.profile**`与`**~/.bashrc**`都具有个性化定制功能，但`**~/.profile**`可以设定本用户专有的路径、环境变量等，它只能登入的时候执行一次。

`**~/.bashrc**`也是某用户专有设定文档，可以设定路径、命令别名，每次 shell script 的执行都会使用它一次。

## 0.1. 差异总结
`**/etc/profile**`，`**/etc/bashrc**` 是系统全局环境变量设定；

`**~/.profile**`，`**~/.bashrc**`用户家目录下的私有环境变量设定。


# Linux基础命令

## 1. 1、查看linux版本

一、查看Linux内核版本命令（两种方法）：

1、cat /proc/version

2、uname -a

二、查Linux系统版本的命令（3种方法）：

1、lsb_release -a，即可列出所有版本信息：

这个命令适用于所有的Linux发行版，包括RedHat、SUSE、Debian…等发行版。

2、cat /etc/redhat-release，这种方法只适合Redhat系的Linux：

[root@S-CentOS home]# cat /etc/redhat-release

CentOS release 6.5 (Final)

3、cat /etc/issue，此命令也适用于所有的Linux发行版。

## 2. 2、yum操作

- 列出已安装软件 `yum list installed | grep []`
- 卸载`yum -y remove java-1.6.0-openjdk*`  //表时卸载所有openjdk相关文件输入
- 查询**`yum -y list java*`**查看可安装java*版本。
- 查询JDK软件包列表`yum search java | grep -i --color jdk`

## 3. 3、文件读写

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



## 4. 4、硬盘拓展

新添加的硬盘无法自动识别需要 
fdisk /dev/sdb然后新建分区，写入
结束后ls /dev/sd* 便可以查看到

openwrt下的mkfs叫mke2fs

### 4.1.1. linux下与存储相关的命令

| free	查看内存使用情况                                  |      |
| --------------------------------------------------------- | ---- |
| df	查看文件系统/挂载点的存储使用情况                   |      |
| mkfs	对磁盘进行格式化                                  |      |
| du	查看某个文件或者目录所占用的存储空间大小            |      |
| fdisk	查看系统所有的磁盘和磁盘分区；对物理磁盘进行分区 |      |

## 4.2. 格式化分区

`fdisk -l`

<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20211015165225482.png" alt="image-20211015165225482" style="zoom: 67%;" />

`fdisk /dev/sdb`

<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/image-20211015165236454.png" alt="image-20211015165236454" style="zoom:67%;" />

## 4.3. LVM简介

LVM全称Logical Volume Manager，逻辑卷管理，在linux系统下对磁盘分区进行管理的机制，是建立在物理磁盘上的抽象层。

物理卷（pv，physical volume）
可以理解为linux下的物理磁盘或者磁盘分区，如/dev/sdb（磁盘）、/dev/sdb1（磁盘分区，通过fdisk对/dev/sdb进行操作划分出来的磁盘分区，类似于windows下的C、D、E盘），但是和物理磁盘以及磁盘分区不同的是，物理卷含有和LVM相关的参数

卷组（vg，volume group）
由一个或者多个物理卷组成的卷组

逻辑卷（lv，logical volume）
通过卷组划分出来的分区，可以用来建立文件系统，例如/dev/mapper/centos-root 是根目录的文件系统挂载点，它就是建立在一个逻辑卷之上的，该文件系统的大小就是这个逻辑卷的大小

物理块（PE，physical extent）
每个物理卷被分为若干个物理块，是LVM寻址中的最小单元，有唯一编号，可以设置大小，默认为4MB

逻辑块（LE，logical extent）
每个逻辑卷被分为若干个逻辑卷，与PE是一一对应的

## 4.4. 案例：centos根目录扩容

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

9.通过 resize2fs 或者 xfs_growfs 命令对文件系统进行扩容

```
[root@slave01 ~]# resize2fs /dev/mapper/centos-root 
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block 当尝试打开 /dev/mapper/centos-root 时
找不到有效的文件系统超级块.
```

这里有时候使用 resize2fs 会出错，查看一下根目录的文件系统

查看*/dev/mapper/centos-root*文件系统

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

## 5. 5、前后台任务切换
前台运行：

```text
$ bash run.sh           
hello world
hello world
hello world
hello world
```

后台运行：

```text
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

### 5.1. 前台运行转后台

很多情况下，我们并不知晓某个任务会运行多久，因此一般在前台运行该任务。如果发现该任务一时半会完成不了，能不能将它从前台运行转到后台？答案是可以，举例说明如何操作：

首先在前台运行run.sh

```text
$ bash run.sh 
hello world
hello world
hello world
```

输入ctrl + z 将该前台任务挂起

```text
^Z
[1]  + 32295 suspended  bash run.sh
```

运行jobs命令，查看任务号(可以看到run.sh对应的任务号是1)

```text
$ jobs                                                                                                  
[1]  + suspended  bash run.sh
```

运行bg命令，将任务从前台转到后台

```text
bg %1
```

### 5.2. 后台运行转前台

既然任务可以从前台转后台，那反过来从后台转前台是否可行呢？答案是可以的 首先以后台运行run.sh

```text
$ nohup bash  run.sh 2>&1 & 
[1] 482
nohup: ignoring input and appending output to 'nohup.out'
```

查看后台任务

```text
$ jobs
[1]  + running    nohup bash run.sh 2>&1
```

运行fg命令，将任务从后台转到前台

```text
$ fg %1                    
[1]  + 482 running    nohup bash run.sh 2>&1
```

## 6. 6、 tar解压缩打包

tar zcfv Desktop.tar.gz Desktop/		打包

tar zxfv hadoop.tar.gz -C /usr/local		解压

功能描述：打包与解打包命令：tar

选项：

-c：打包；

-x：解打包；

-f：指定压缩包的文件名。压缩包的扩展名是用来给管理员识别格式的，

-v：显示打包文件过程；

-C 目录：指定解打包位置；

-z：压缩和解压缩 ".tar.gz"格式

-P ：可以使用绝对路径来压缩









