---
title: PVE
tags:
  - PVE
  - 虚拟机
  - linux操作
date created: 2023-04-30
date modified: 2025-01-20
---
[PVE官方手册](https://pve-doc-cn.readthedocs.io/zh-cn/latest)

[【NAS】PVE的简单使用教程、调优、常见问题汇总 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/614820460)

[[转]ProxmoxVE(PVE) 第三方工具PveTools功能介绍及安装运行 - dirgo - 博客园 (cnblogs.com)](https://www.cnblogs.com/dirgo/p/17115229.html)

[PVE详细安装op、ikuai教程，含修改国内源、直通、更新内核-软路由,x86系统,openwrt(x86),Router OS 等-恩山无线论坛 - Powered by Discuz! (right.com.cn)](https://www.right.com.cn/forum/thread-8106799-1-1.html)

[(教程贴) 如何在iKuai软路由系统设置阿里云DDNS - 哔哩哔哩 (bilibili.com)](https://www.bilibili.com/read/cv2466921)

[(145条消息) ProxmoxVE 7.0 LXC下搭建openwrt软路由_lxc openwrt_kangzeru的博客-CSDN博客](https://blog.csdn.net/kangzeru/article/details/115373587)

[pve安装docker好去处，再谈lxc安装docker，顺带安装docker中文管理界面_软件应用_什么值得买 (smzdm.com)](https://post.smzdm.com/p/a30km0k5/)

# PVE断电故障

![](服务器相关/attachments/PVE.png)
![](服务器相关/attachments/PVE-1.png)
ikuai
![](服务器相关/attachments/PVE-2.png)
openwrt
![](服务器相关/attachments/PVE-3.png)


# 13.12 Proxmox VE端口列表

- Web界面：8006
- VNC控制台：5900-5999
- SPICE proxy：3128
- sshd（用于集群管理）：22
- rpcbind：111
- corosync多播（集群通信使用）：5404, 5405 UDP

# 网络类型

net2
```shell
root@iStoreOS:~# pystun3
NAT Type: Restric NAT
External IP: 117.163.200.199
External Port: 27827
Press any key to continue
```

# 获取公网IP6

[Proxmox网桥通过SLAAC配置公网ipv6地址 - 海运的博客 (haiyun.me)](https://www.haiyun.me/archives/1416.html#comment-688)
Proxmox安装后默认没有通过SLAAC配置公网ipv6地址，使用debian/ubuntu的方法配置ipv6提示错误不支持的方法auto。
需要将accept_ra值改成2才能自动配置SLAAC ipv6地址：
`nano /etc/sysctl.conf`

`net.ipv6.conf.all.accept_ra=2`
`net.ipv6.conf.default.accept_ra=2`
`net.ipv6.conf.vmbr0.accept_ra=2`
`net.ipv6.conf.all.autoconf=1`
`net.ipv6.conf.default.autoconf=1`
`net.ipv6.conf.vmbr0.autoconf=1`

# PVETools

方式一：命令行安装

> 需要用root账号来运行

在终端中按行分别执行以下内容：

> 强烈建议先删除企业源：`rm /etc/apt/sources.list.d/pve-enterprise.list`

```
export LC_ALL=en_US.UTF-8
apt update && apt -y install git && git clone https://github.com/ivanhao/pvetools.git\
cd pvetools
chmod +x ./*.sh
./pvetools.sh
```

# 网络拓扑

![Snipaste_2023-05-06_12-29-49.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/Snipaste_2023-05-06_12-29-49.png)

# 删除硬盘

[Proxmox VE(PVE) 解决无法删除无效设置一例 | Bug侠 (bugxia.com)](https://bugxia.com/2335.html)

# 硬盘直通

[LXC直通硬盘 (mereith.com)](https://www.mereith.com/post/86)

[Proxmox VE(PVE)添加硬盘详解 – late哥哥笔记 (lategege.com)](https://www.lategege.com/?p=538)

[玩转PVE:给黑群晖进行硬盘直通 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/540846901)

[许迎果 第194期 给PVE平台的群晖NAS虚拟机直通硬盘 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/385048123)

一、首先安装成功一个黑裙

二、需要将直通给黑裙的硬盘添加到pve虚拟机中

三、通过ssh工具连接到pve虚拟机**或者**直接在pve的shell终端执行，查看PVE系统下所有的磁盘存储设备并查看磁盘ID序列号：

```text
ls -l /dev/disk/by-id/
```

四、通过下面的命令将当个硬盘直通个虚拟机

```shell
qm set 101 -sata3 /dev/disk/by-id/ata-TOSHIBA_MQ04ABF100_Y94ETIZ4T
```

# LXC硬盘直通

本处的出发点是，[pve](https://so.csdn.net/so/search?q=pve&spm=1001.2101.3001.7020)中有一块磁盘已挂载用于网络共享，但其中的文件需要被docker某些程序访问。为了减少污染pve环境，就通过lxc安装docker，再把目录挂载到docker里面去。

```
标准格式：pct set -mp1 /video,mp=/video

实际案例：pct set 105 -mp1 /mnt/,mp=/home/jellyfin/media/st2

pct set 103 -mp1 /mnt/pve/toshiba,mp=/mnt/toshiba
```
重启容器。

逐个介绍：
pct set： 在pve中运行
105： 自己的容器ID
-mp1： 第一个文件夹，如果挂载到第二、三个，用mp2，mp3依次递增。不记得设置了多少个，在pve对应的ID中，里面有资源选项卡会显示挂载的情况。
/mnt/st2： 这是pve上的目录位置
mp=/home/jellyfin/media/st2： 这是要挂载到容器中的目录位置

**推荐！！**
除了上述方法，还可以直接在PVE中进行编辑。

```shell
nano /etc/pve/lxc/id.conf
mp1: /mnt/st2,mp=/home/jellyfin/media/st2
```

# 强制关闭虚拟机

  在日常使用PVE搭建虚拟机的使用过程中，偶尔会出现虚拟机假死无法关闭的情况并提示TASK ERROR: VM quit/powerdown failed – got timeout显示关闭推出无反应超时，通过查找学习可以使用以下办法强制关闭假死的虚拟机。

      1、选中右侧pve，点击左上方shell，打开ssh控制台，通过ps命令查到对应虚拟机的VM进程号。

```
ps -ef|grep "/usr/bin/kvm -id 102" |grep -v grep
```

# 换源

```
纯代码
apt install apt-transport-https ca-certificates
sed -i 's|^deb http://ftp.debian.org|deb https://mirrors.ustc.edu.cn|g' /etc/apt/sources.list
sed -i 's|^deb http://security.debian.org|deb https://mirrors.ustc.edu.cn/debian-security|g' /etc/apt/sources.list
source /etc/os-release
echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/pve $VERSION_CODENAME pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
apt update
cp /usr/share/perl5/PVE/APLInfo.pm /usr/share/perl5/PVE/APLInfo.pm_back
sed -i 's|http://download.proxmox.com|https://mirrors.ustc.edu.cn/proxmox|g' /usr/share/perl5/PVE/APLInfo.pm
systemctl restart pvedaemon
```

[更换 PVE7 软件仓库源和 CT模板（LXC）源为国内源 | Frytea](https://www.frytea.com/archives/605/)

## 替换 apt 软件源

替换前建议先更新下证书，否则可能由于证书不可用导致 https 无法使用，进而无法下载所有软件。

```bash
$ sudo apt install apt-transport-https ca-certificates
```

首先替换通用软件源， Debian 的软件源配置文件是 `/etc/apt/sources.list`，备份后将其中内容修改为以下即可。

```bash
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
```

之后替换 pve 软件源，pve 镜像默认的 pve 软件源配置文件是 `/etc/apt/sources.list.d/pve-enterprise.list` ，备份后将其中内容替换为以下即可：

```bash
deb https://mirrors.tuna.tsinghua.edu.cn/proxmox/debian bullseye pve-no-subscription
```

最后更新下，速度很快：

```bash
sudo apt-get update
```

## 修改 **CT Templates (LXC容器)源**

将 `/usr/share/perl5/PVE/APLInfo.pm` 文件中默认的源地址 `http://download.proxmox.com` 替换为 `https://mirrors.tuna.tsinghua.edu.cn/proxmox` 即可。

可以使用如下命令修改：

```bash
cp /usr/share/perl5/PVE/APLInfo.pm /usr/share/perl5/PVE/APLInfo.pm_back
sed -i 's|http://download.proxmox.com|https://mirrors.tuna.tsinghua.edu.cn/proxmox|g' /usr/share/perl5/PVE/APLInfo.pm
```

针对 `/usr/share/perl5/PVE/APLInfo.pm` 文件的修改，重启后生效。

```bash
systemctl restart pvedaemon.service
```

之后在 pve 网页端下载 CT Templates 速度就很快了。

## 更新系统

```
apt install -y pve-kernel-6.2.6-1-pve pve-headers-6.2.6-1-pve pve-firmware

```

# 启用硬件直通

1、打开PVE节点的shell，输入命令：
```
nano /etc/default/grub

GRUB_CMDLINE_LINUX_DEFAULT="quiet"
改为：
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
```
编辑完文件后按“Ctrl + X” > “Y” > “回车”，继续输入以下命令。

2、在PVE的shell中输入：
nano /etc/modules
在文件下面添加新内容
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd 
```
编辑完文件后按“Ctrl + X” > “Y” > “回车”，继续输入以下命令。

3、最后更新配置信息并重启PVE主机
```
update-grub
update-initramfs -u -k all
reboot
```

查询网卡信息
```
lspci | grep -i ethernet
```

编辑虚拟机配置

vi /etc/pve/nodes/pve/qemu-server/101.conf

创建虚拟机

```
qm importdisk 100 /var/lib/vz/template/iso/istoreos-21.02.3-2023042111-x86-64-squashfs-combined.img local-lvm

qm importdisk 102 /var/lib/vz/template/iso/synoboot.img local-lvm
```

# AC2100

![[attachments/31d53df3e3fbbbeaa29ba126d86bff48_MD5.png]]

# samba

## 1、安装samba服务

```bash
apt update && apt install samba -y
```

## 2、设置配置文件

可以在/etc/samba/smb.conf末尾中添加

```basic
#添加一个iso共享库
[toshiba]                                  //此处是路径，例如//10.13.14.2/iso
   comment = toshiba             //描述
   path =  /mnt/sata        //共享的文件夹
   guest ok = no                       //不允许访客
   browseable = no                     //不允许浏览
   write list = root                   //运行root读写
```

懒人可以一键开启

```bash
cat >>/etc/samba/smb.conf <<EOF
[toshiba]                                 
   comment = this is a toshiba           
   path =  /mnt   
   guest ok = no                   
   browseable = no                  
   write list = root 
EOF
```

## 3、添加用户

```bash
smbpasswd -a root
```

## 4、重启smb服务

```bash
systemctl restart smbd 
```

# LXC容器

查看所有可用模板
```javascript
pveam available
```
查看所有可用系统模板
```javascript
pveam available --section system
```
查看所有可用应用模板
```javascript
pveam available --section turnkeylinux
```
下载模板到存储，如下载tomcat15到local：
```javascript
pveam download local debian-9-turnkey-tomcat_15.1-1_amd64.tar.gz
```
查看存储local中的模板
```javascript
pveam list local
```

## 1. 管理容器

一般来说，管理linux[服务器](https://cloud.tencent.com/product/cvm?from=20065&from_column=20065)通过ssh登录操作，不过lxc可以直接从宿主机执行pct enter CTID进入LXC的shell:

```javascript
pct list
pct enter 123
```

因为PVE基于debian，所以通常使用的应用模板也是基于debian的，安装完成后需要修改默认时间:

```javascript
pct enter 123
dpkg-reconfigure tzdata
```

LXC下面遇到的权限问题可以通过调整Cgroup来解决。

# 显示CPU硬盘温度

hddtemp /dev/sd?和sensors命令可直接查看温度
![](https://www.right.com.cn/FORUM/data/attachment/forum/202206/26/231023nmzbvwxvx8bdb88x.jpg)
显示CPU主频+核心频率、CPU温度、NVME硬盘温度、SATA硬盘温度，按压缩包路径替换即可。

如果有多个硬盘，自行修改页面高度。
编辑文件 /usr/share/pve-manager/js/pvemanagerlib.js，
跳转到 widget.pveNodeStatus 位置
height: 455,
红字部分改大即可。

如果是全新安装或者首次更新并使用 PVE 7.2+，需要安装工具并重启 pveproxy 服务：
apt-get install -y lm-sensors && chmod +s /usr/sbin/smartctl && systemctl restart pveproxy
如果是更新脚本，只需要重启 pveproxy 服务：
systemctl restart pveproxy

**还原方法：**
执行命令重新安装 proxmox-widget-toolkit 和 pve-manager
apt reinstall proxmox-widget-toolkit pve-manager

N5105 调整CPU模式
之前7.2.1 N5105的CPU模式只支持performance powersave2种
更新到最新版本内核之后CPU模式支持conservative, ondemand, userspace, powersave, performance, schedutil
1. apt install cpufrequtils lm-sensors

2. nano /etc/default/cpufrequtils
显示：
ENABLE="true"
GOVERNOR="ondemand"
MAX_SPEED="2000000"
MIN_SPEED="800000"

红字部分默认的是performance，我这里改成了ondemand 大家可以根据自己的需要修改
平时以低速方式运行，当系统负载提高时候自动提高频率。以这种模式运行不会因为降频造成性能降低，同时也能节约电能和降低温度。
MAX_SPEED是上限，MIN_SPEED是下限，默认是1600000，我这里改成了800000
最后的效果是，CPU自动在0.8G和2G之间变频，CPU占用12%左右，频率保持在1G上下浮动
调整前最低是2G，CPU占用10%左右，经常酷睿到2.8G，调整CPU模式后，温度从原来43左右，降到了37左右

3. systemctl restart cpufrequtils
重启服务后生效。

# Docker

一键安装Docker

```
apt install curl -y && curl -sSL https://get.daocloud.io/docker | sh
```

[Debian 11 / Ubuntu 22.04 安装 Docker 以及 Docker Compose 教程 - 烧饼博客](https://u.sb/debian-install-docker/)
图形管理工具
```
#汉化版portainer
portainer-ce 
docker run -d --restart=always --name="portainer" -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data 6053537/portainer-ce
```

# Zerotier

Debian系统下安装ZeroTier组件异地局域网，实现内网穿透！

1.登录Debian系统，切换root账号

su root

2.安装ZeroTier

curl -s https://install.zerotier.com/ | bash

如果看见Successful就ok了！

3.填写网络ID，加入异地虚拟网络

zerotier-cli join (网络ID)

如果看见200 join OK字样就说明成功加入异地虚拟局域网了。
