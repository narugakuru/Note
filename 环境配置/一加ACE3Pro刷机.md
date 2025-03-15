---
title: 一加ACE3Pro刷机
tags:
  - 
date created: 2025-02-25
date modified: 2025-02-26
---

# 一加ACE3Pro刷机

- [一加手机解锁BL获取ROOT完美隐藏环境教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1n6fVYFEGD/?buvid=XXFC908F937439BBEC4463F91CC9B98D7C1F4&from_spmid=default-value&is_story_h5=false&mid=RWkn5qBm1mU8WTGiNDK7nw%3D%3D&plat_id=114&share_from=ugc&share_medium=android&share_plat=android&share_session_id=1bb7eca0-a6aa-4889-888a-0762337fcff9&share_source=COPY&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1737704921&unique_k=eWug9Zq&up_id=1203280519&vd_source=2bc27ec22a8e739492a12db57107f831)
- [一加全能盒子、一加全能工具箱最新版下载 - 一加全能盒子 | 工具箱](https://optool.daxiaamu.com/optool/)
- https://optool.daxiaamu.com/wiki_pctool
- [一加OnePlus ACE 3 Pro - 一加手机官方ROM - 一加手机官方ROM下载](https://yun.daxiaamu.com/OnePlus_Roms/%E4%B8%80%E5%8A%A0OnePlus%20ACE%203%20Pro/)
- [一加平板Pro、ACE 3 Pro、一加12及其他型号手机一键ROOT视频教程 - 大侠阿木博客](https://www.daxiaamu.com/7886/)
- [ColorOS14升级15保持ROOT - 阿牛 - 博客园](https://www.cnblogs.com/evlon/p/18660403/coloros14-15)

## Root方案

一加平板Pro、ACE 3 Pro、一加12及其他型号刷面具Magisk步骤
视频教程：[https://www.bilibili.com/video/BV1gm421G7jB/](https://www.bilibili.com/video/BV1gm421G7jB/)

 一加平板Pro、ACE 3 Pro、一加12及其他型号刷KernelSU教程
视频教程：[https://www.bilibili.com/video/BV1gm421G7jB/](https://www.bilibili.com/video/BV1gm421G7jB/)
图文教程：[https://optool.daxiaamu.com/install_kernelsu](https://optool.daxiaamu.com/install_kernelsu)

## 模块

- [ROOT后可以做什么 - 一加全能盒子 | 工具箱](https://optool.daxiaamu.com/whattodowithroot)
- [一加Ace3 ColorOS 14 完美攻略：一键解BL+ROOT教程与精选模块推荐 – 白云博客-白云博客](https://www.bybk.cc/2282.html)
- [Momo使用指南与问题解决大全：KernelSU、APatch、隐藏Root、TEE损坏、ART参数、SELinux、Zygote注入、调试模式修复手册 – 白云博客-白云博客](https://www.bybk.cc/2400.html)
- [LSPosed模块-LSPosed中文网](https://lsposed.cn/lsposed%e6%a8%a1%e5%9d%97)


先开启开发者模式，多次点击版本号信息即可。打开OEM解锁和USB调试，连接手机后选择文件传输模式

！注意，每次手机都要选择文件传输模式！

0、Windows终端设置、关闭安全、安装Fastboot驱动、下载相关APK、开源软件（网盘，可见评论）、官方完整rom包（7G左右）
1、用大侠阿木按2解锁BL（期间需要在手机上点一次电源键确认）
2、用大侠阿木按P刷入KSUNext或者其他框架（注意手机也要点安装APK）
3、安装主要的几个开源包
	PlayIntegrityFix
	Shamiko
	Tricky-Store
	LSPosed
	Zygisk-Next（先安装这个）
	
4、安装MT管理器
5、给MT超级用户权限
6、给Tricky-Store复制keybox.xml和sh脚本
7、安装各种软件，包括隐藏应用列表、秘钥认证、ruru、牛头、momo、memorydetector、指纹支付
8、打开ksu，启用隐藏应用列表、指纹支付
9、回到ksu，重启一下手机
10、到隐藏应用列表里面，在下面备份与还原导入hma config，然后点击管理，确认权限
下拉菜单激活LSPosed，然后把两个模块启用
11、回到MT运行sh脚本，要记得勾选ROOT方式运行，由于此时momo会提示tee损坏，所以要选择N确定
完成