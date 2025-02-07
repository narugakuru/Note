---
tags:
  - 网络
  - linux操作
---

# Docker网络配置
## 1. Docker访问外网
![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302052036616.png)

-   （1）busybox发送ping包：172.17.0.2 > www.bing.com。
-   （2）dockero收到包，发现是发送到外网的，交给NAT处理。
-   （3）NAT将源地址换成ens192的IP：10.0.2.15 >www.bing.com.
-   （4）ping从 ens192出去，达www.bing.como

## 2. 外网访问Docker
**Docker默认关闭ip6，需手动打开！！！**
先划分ip
2409:8a38:5401:42d3::/66
2409:8a38:5401:42d3:4000::/66
2409:8a38:5401:42d3:8000::/66
2409:8a38:5401:42d3:c000::/66

创建/etc/docker/daemon.json，把一个子网分给docker
```json
{  
  "experimental": true,  
  "ipv6": true,  
  "ip6tables": true,  
  "fixed-cidr-v6": "2409:8a38:5401:42d3::/66"  
}
```
重启docker，完事。


# 硬盘管理
[[Linux相关#4、硬盘拓展]]

[Linux 硬盘分区、分区、删除分区、格式化、挂载、卸载 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1504165#:~:text=Linux%20%E7%A1%AC%E7%9B%98%E5%88%86%E5%8C%BA%E3%80%81%E5%88%86%E5%8C%BA%E3%80%81%E5%88%A0%E9%99%A4%E5%88%86%E5%8C%BA%E3%80%81%E6%A0%BC%E5%BC%8F%E5%8C%96%E3%80%81%E6%8C%82%E8%BD%BD%E3%80%81%E5%8D%B8%E8%BD%BD%201%201.%E5%88%9B%E5%BB%BA%E5%88%86%E5%8C%BA%20%E5%85%88%E6%9F%A5%E7%9C%8B%E4%B8%8B%E6%98%AF%E5%90%A6%E6%9C%89%E7%A3%81%E7%9B%98%E6%B2%A1%E6%9C%89%E5%88%86%E5%8C%BA%20fdisk%20-l%20%E5%85%B6%E4%B8%AD%E7%AC%AC%E4%B8%80%E4%B8%AA%E6%A1%86%E5%92%8C%E7%AC%AC%E4%BA%8C%E4%B8%AA%E6%A1%86%EF%BC%8C%E6%98%AF%E5%B7%B2%E7%BB%8F%E5%88%86%E5%A5%BD%E5%8C%BA%E7%9A%84%E4%B8%8D%E5%90%8C%E7%A3%81%E7%9B%98%E3%80%82,%2Fsdb%20m%20d%203%20w%20%E8%BE%93%E5%85%A5%20w%20%E4%BF%9D%E5%AD%98%EF%BC%8C%E8%BF%99%E4%B8%AA%E6%97%B6%E5%80%99%E5%88%86%E5%8C%BA%E4%BB%A5%E5%8F%8A%E5%88%A0%E9%99%A4%E4%BA%86%EF%BC%8C%E5%8F%AF%E4%BB%A5%E9%87%8D%E6%96%B0%E5%88%9B%E5%BB%BA%E4%BA%86%E3%80%82)
[记录vmware虚拟主机给openwrt增加虚拟磁盘扩容过程](https://mailberry.com.cn/2022/12/vmware-openwrt-add-disk/)
[Linux 新建扩展分区拓展4个以上分区 - 简书 (jianshu.com)](https://www.jianshu.com/p/b38963278713)

df -h 查看磁盘挂载 及使用情况
ls /dev/sd* 列出所有磁盘
lsblk 查看系统分区树


## 1. 创建分区
- 选择硬盘 cfdisk /dev/sdb
- 选择分区GPT
- 选择new一个分区
- write写入分区 /dev/sdb
- (sdb* 是属于sdb硬盘的一个分区)

## 2. 格式化
mkfs.ext4 /dev/mmcblk1
**openwrt下的mkfs叫mke2fs**

## 3. 挂载
- blkid /dev/sdb 或者 mount /dev/sdb /opt (docker缓存目录)
- 然后openwrt后台挂载磁盘

## 4. 挂载卸载不掉 umount target is busy
### 4.1. 使用fuser命令处理

安装fuser命令
	yum install psmisc 
查看在使用的进程
	 fuser -mv /mnt/
杀死占用的进程，
	kill -9 13830
发现 bash 退出了，新开shell并再次查看
	[ root@server-10 ~ ] # fuser -mv /mnt/                     
	USER        PID ACCESS COMMAND/mnt:                
	root     kernel mount /mnt
确认无进程连接后，使用卸载命令
	umount /mnt/

注意：  
**可以使用 fuser -km /mnt 进行 kill 进程, 可能会结束当前bash进程**  
可以使用 kill 命令杀掉查到对应的进程 。  
强制 kill 进程可能会导致数据丢失，请确保数据得到有效备份后，再进行相关操作。


## 5. 创建4个以上分区
Linux分区表只能存储4个分区，想要创建4个以上则必需借助拓展分区e

1. 创建3p+1e
2. e分配32m，用来当第二个分区表
3. 然后就可以创建5-16号分区
4. 创建swap分区
	1. mkswap /dev/mmcblk1p5
	2. swapon /dev/mmcblk1p5
5. 不要在ssh挂载硬盘，进入openwrt网页后台设置自动挂载，只用它默认的挂载设置，重启（避免以后重启挂载到别的地方）

## 6. 创建swap分区
mkswap /dev/mmcblk1p5
swapon /dev/mmcblk1p5

# 常用命令

## 1. 安装tar.gz文件

1. 解压

	tar -xvzf file
2. 检查编译

	./configure 
3. 编译

	make
4. 编译后安装

	make install
5. 清除临时文件

	make clean
	make distclean


## 2. 硬件相关命令

``查看CPU信息：
top
`cat` `/proc/cpuinfo`

查看内存使用情况：
`cat` `/proc/meminfo`

查看磁盘的使用率：
`df` `-h```

## 3. 系统相关命令  

查看内核信息：
`uname` `-a`

读取内核的日志：
`dmesg`

读取系统日志：
`logread`

列出进程：
`ps` `-w`

显示运行时间、CPU负载：
`uptime`

## 4. OPKG包管理

更新可用软件包列表
`update`

查找软件包：
`opkg list |``grep` `xxx` `# xxx 就是你想要找的包`

列出所有安装的包：
`opkg list-installed`

查看包的信息：
`opkg info xxx`

查看包的文件：
`opkg files xxx`

## 5. 无线相关

查看无线网卡的信息：
`iwinfo wlan0 info`

重启无线服务：
`wifi down` 
`wifi up`

扫描热点：
`iw dev wlan0 scan`

查看设备连接的客户端：
`iwinfo wlan0 assoclist`  
`iw dev wlan0 station dump`



# Window子系统

在windows功能中重新勾选hyper-v
然后开启hyper-v模式  
在管理员powershell中执行

```cobol
bcdedit /set hypervisorlaunchtype auto
```
## 1. 添加策略组管理器
```shell
@echo off

pushd "%~dp0"

dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientExtensions-Package~3*.mum >List.txt

dir /b C:\Windows\servicing\Packages\Microsoft-Windows-GroupPolicy-ClientTools-Package~3*.mum >>List.txt

for /f %%i in ('findstr /i . List.txt 2^>nul') do dism /online /norestart /add-package:"C:\Windows\servicing\Packages\%%i"

pause
```
如果禁用了组策略里面的Device Guard[虚拟化](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E5%8C%96&spm=1001.2101.3001.7020)安全设置，  
需要打开组策略管理，  
本地计算机策略 > 计算机配置 > 管理模板>系统 > Device Guard  
设置 基于虚拟化的安全设置为“已开启”


## 2. wsl创建openwrt
```text
wsl  --import openwrt d:\wsl\openwrt\ D:\wsl\openwrt\openwrt-21.02.2-x86-64-rootfs.tar.gz
```

## 3. services.msc设置hyperv自启动

## 4. 运行openwrt on wsl2

1. 重新运行`Windows Terminal`，选择标签最后的下拉菜单，即可看到 openwrt 虚拟容器，点击即可运行。

2. 由于wsl接管了init程序，因此我们启动了一个最精简的openwrt,除了ash什么也没有运行。运行 `ps`命令可查看。

3. 通过 `cat /etc/os-release` 可以看到openwrt版本号

## 5. 运行相关服务并启动web管理

1. 建立相关目录，命令如下：

```text
   mkdir -p /tmp/lock
   mkdir -p /tmp/resolv.conf.d
   mkdir -p /tmp/dnsmasq.d
   mkdir -p /tmp/log
   mkdir -p /tmp/run/rpcd
   mkdir -p /tmp/run/ddns
   mkdir -p /tmp/run/collectd
   mkdir -p /tmp/run/dnsmasq
   mkdir -p /tmp/shm
   mkdir -p /tmp/etc
   mkdir -p /tmp/spool/cron
   mkdir -p /tmp/cache/netdata
   mkdir -p /tmp/hosts
   mkdir -p /tmp/sysinfo
   mkdir -p /tmp/state
   mkdir -p /tmp/tmp
```

2. 以后每次进入openwrt 运行以下命令启动服务：

```text
    /sbin/procd &
    /sbin/ubusd &
    service network start
    service rpcd start
    service uhttpd start
    opkg install luci-i18n-base-zh-cn

    ifconfig eth0
```

1. 由于 wsl2 每次进入之后，ip地址都不一样，因此最后一条命令得到的结果也不一样，需要在浏览器中打开相应的地址才能进入web管理界面。

## 6. Alpine安装JDK
[[学习,记录,骚操作] 在openwrt上搭建MC服务器 | yingye's Blog](https://blog.yingye.site/2021/04/26/%E5%9C%A8openwrt%E4%B8%8A%E6%90%AD%E5%BB%BAmc%E6%9C%8D%E5%8A%A1%E5%99%A8)

openwrt能安装alpine linux的包管理器`apk`，还因为alpine和openwrt都是使用的musl libc库的原因，能正常使用由apk安装的软件。

先安装apk

```shell
opkg update && opkg install apk alpine-keys alpine-repositories screen
```
screen用于新建一个shell窗口，保证在断开连接后，服务器依旧能运行。  

更新源，并安装java环境

```
apk update && apk add openjdk11-jre
```

要是有特殊需求也可以安装完整的jdk

```
apk add openjdk11-jdk
```
