---
title: OpenWrt网络配置
tags:
  - linux操作
  - ddns
  - 计算机网络
date created: 2023-05-27
date modified: 2025-01-20
---
[[Linux相关]]



# 机器IP

255.255.255.0
- AC2100
	- 192.168.31.1 / 192.168.31.179
- openwrt
	- 192.168.31.2
- PVE
	- 192.168.31.3
- ikuai
	- 192.168.31.5
- R7000P
	- 192.168.31.4

ETH0做为管理口，其他的网卡直通ikuai

| 物理接口 | PVE标记 | PCI | PVE | OP | ikuai |
| ---- | ---- | ---- | ---- | ---- | ---- |
| ETH0 | enp1s0 | 01:00.0 | 接光猫 | 桥接wan、lan | 桥接lan |
| ETH1 | enp2s0 | 02:00.0 | 直通ikuai |  |  |
| ETH2 | eno1 | 03:00.0 | 直通ikuai |  |  |
| ETH3 | enp4s0 | 04:00.0 | 管理口接AP |  | 直通拨号wan |

# docker代理

```
vi /etc/systemd/system/multi-user.target.wants/docker.service

然后在service下面加入代理的配置，比如：
Environment=HTTP_PROXY=http://root:passwd@192.168.56.1:1080
Environment=HTTPS_PROXY=http://root:passwd@192.168.56.1:1080
Environment=NO_PROXY=localhost,127.0.0.1

Environment=HTTP_PROXY=http://127.0.0.1:8849
Environment=HTTPS_PROXY=http://127.0.0.1:8849
Environment=NO_PROXY=localhost,127.0.0.1


重启服务
systemctl daemon-reload
systemctl restart docker

```

# PVE配置记录

### openwrt

[X86 PVE 安装 | 易有云产品中心 (linkease.com)](https://doc.linkease.com/zh/guide/istoreos/install_pve.html)

- 默认IP http://192.168.100.1
- 默认密码：password
- 如果只有一个网口，默认的网口是 LAN；如果大于一个网口，默认 eth0 是 WAN 口，其它都是 LAN
- 如果要修改 LAN 口 IP，首页有个内网设置，或者命令行用 quickstart 命令修改
- 远程管理k1808962ek

[[学习,记录,骚操作] 在openwrt上搭建MC服务器 | yingye's Blog](https://blog.yingye.site/2021/04/26/%E5%9C%A8openwrt%E4%B8%8A%E6%90%AD%E5%BB%BAmc%E6%9C%8D%E5%8A%A1%E5%99%A8)

### 网口对应PCI

```
root@pve:~/pvetools# lspci | grep -i 'eth'
01:00.0 Ethernet controller: Intel Corporation Device 125c (rev 04)
02:00.0 Ethernet controller: Intel Corporation Device 125c (rev 04)
03:00.0 Ethernet controller: Intel Corporation Device 125c (rev 04)
04:00.0 Ethernet controller: Intel Corporation Device 125c (rev 04)
```

### 系统代理

```
vim /etc/profile
http_proxy=http://192.168.31.18:7000
https_proxy=http://192.168.31.18:7000
ftp_proxy=http://192.168.31.18:7000
export http_proxy
export https_proxy
export ftp_proxy
```

### 配置clash开机自启动

**1.创建service文件**
`touch /etc/systemd/system/clash.service`
**2.编辑service文件**
打开service文件
`vi /etc/systemd/system/clash.service`
填入以下内容**(注意修改clash文件夹路径)**
[Unit] Description=clash daemon [Service] Type=simple User=root ExecStart=/opt/clash/clash -d /opt/clash/ Restart=on-failure [Install] WantedBy=multi-user.target
保存并退出
3.重新加载systemctl daemon
`systemctl daemon-reload`
4.启动Clash
`systemctl start clash.service`
5.设置Clash开机自启动
`systemctl enable clash.service`
**以下为Clash相关的管理命令**
启动Clash
`systemctl start clash.service`
重启Clash
`systemctl restart clash.service`
查看Clash运行状态
`systemctl status clash.service`

# DDNS

## 1. 外网不能访问内网

内网ipv6可以互相ping通，可以访问外网，外网不能访问内网。
原因：软路由接口没有提供dhcp服务

## 2. 内网不能连接bug

<img src="https://cdn.jsdelivr.net/gh/narugakuru/images/img/202302010849326.png" alt="image.png" style="zoom:33%;" />

更改IP6端口
vi /etc/config/uhttpd
修改第四行的监听端口443为444
/etc/init.d/uhttpd restart

## 3. 获取光猫超级用户和密码

用户名：CMCCAdmin
超级密码：aDm8H%MdA
网络远程管理认证密码：k1808962ek
宽带：18707976607 360724

## 4. 关闭防火墙

1.  阻止防火墙服务开机自动启动
/etc/init.d/firewall disable

2. 关闭防火墙
/etc/init.d/firewall stop

## 5. DDNS启动问题

sleep 60 && /usr/lib/ddns/dynamic_dns_updater.sh -v 0 -S 你的ddns任务名称 -- start&

# 使用NAT模式配置网络

1. 虚拟机网络
- IP：192.168.1.6
- 网关gateway：192.168.1.1
- DNS：114.114.114.114（指向宿主机）

2. 宿主机适配器
- IP：192.168.1.2
- 网关gateway：192.168.1.1
- DNS：114.114.114.114和223.5.5.5

3. 编辑网络
vi /etc/config/network

4. 重启网络
source /etc/config/network
service network restart

**5. 防火墙**

```
1.  阻止防火墙服务开机自动启动

/etc/init.d/firewall disable

2. 关闭防火墙

/etc/init.d/firewall stop
```

6.编辑openwrt的接口
- IP：192.168.1.6
- 网关gateway：192.168.1.1
- DNS：114.114.114.114（指向宿主机）
- 禁用DHCP服务

7. 更新软件包

```
更新软件列表 
opkg update 

更新所有 LUCI 插件 
opkg list-upgradable | grep luci- | cut -f 1 -d ' ' | xargs opkg upgrade 

如果要更新所有软件，包括 OpenWRT 内核、固件等 
opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade
```

快速搜索查找
方法一：
locate redis-server
/usr/local/bin/ 就是redis的安装位置

方法二: whereis
whereis redis-server

方法三: which
which redis-server

方法四: find
find / -name redis-server
