---
title: iStore allInOne方案-2501
tags:
  - 
date created: 2025-01-22
date modified: 2025-01-25
---
所有软件尽量以docker部署，不影响物理机，方便备份回溯


[【小白向】使用Cloudflare搭建无服务器无成本域名邮箱 - 鱼のBlog](https://blog.fishze.com/archives/287)
[OpenWRT 软件源整理 - Oskyla 烹茶室](https://frytea.com/archives/1354/)
[官方openwrt源、luci源仓库-软路由,x86系统,openwrt(x86),Router OS 等-恩山无线论坛](https://www.right.com.cn/FORUM/thread-5837022-1-1.html)
[Index of /releases/22.03.6/targets/x86/generic/](https://archive.openwrt.org/releases/22.03.6/targets/x86/generic/)
https://op.dllkids.xyz/packages/x86_64

# 机器IP

255.255.255.0
- AC2100中继
	- 192.168.31.1 / 192.168.31.179
- openwrt
	- 192.168.31.2
	- DHCP服务器
	- 拨号
	- DDNS
- R7000P
	- 192.168.31.10

![](服务器相关/软路由/attachments/iStore%20allInOne方案-2501.png)
