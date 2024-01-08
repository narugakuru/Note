---
tags:
  - linux操作
---

Hadoop3.3.4
jdk1.8.0_351


## 1. 网络设置


	vi /etc/NetworkManager/system-connections/ens160.nmconnection

Net模式配置静态ip
```
address1=192.168.1.128/24,192.168.1.2
meth=manual
```

#### 1.1.1. 关闭防火墙命令：
	【命令】systemctl stop firewalld
	【命令】systemctl disable firewalld

修改主机名
hostnamectl set-hostname <主机名>
reboot
shutdonw -r now

### 1.2. 使用 nmcli 命令配置静态 IP 地址
[NetworkManager服务（nmcli） - 掘金 (juejin.cn)](https://juejin.cn/post/7112085821519790094#heading-0)

nmcli 是一个命令行实用程序，用于在 Fedora Linux 上配置静态 IP 地址。要使用该命令，必须首先打开一个终端窗口(Ctrl+Alt+T)。然后，您需要输入以下命令

```
$ nmcli connection show
$ nmcli
Fedora 系统连接到[调制解调器](https://so.csdn.net/so/search?q=%E8%B0%83%E5%88%B6%E8%A7%A3%E8%B0%83%E5%99%A8&spm=1001.2101.3001.7020)并从 DHCP 服务器获得 IP。现在，要将 IP 地址设置为静态和持久的，请运行以下 nmcli 命令。
```

修改名称为 Wired connection 1 的连接，运行以下命令添加静态 ip 地址和网关 ip

```
$ sudo nmcli con modify 'Wired connection 1' ifname ens160 ipv4.method manual ipv4.addresses 192.168.1.166/24 gw4 192.168.1.1
```

添加 DNS IP 地址，执行命令

```
$ sudo nmcli con mod 'Wired connection 1' ipv4.dns 192.168.1.1
```

要使上述更改生效，请禁用并启用连接

```
$ sudo nmcli con down 'Wired connection 1'
$ sudo nmcli con up 'Wired connection 1'
```

使用 ip 命令验证 ip 地址

	$ ip a s

如果您不想使用现有的连接，而要创建新的连接来配置静态 IP 地址，请依次执行以下命令

```
$ sudo nmcli con add type ethernet con-name 'static-fedora' ifname enp0s3 ipv4.method manual ipv4.addresses 192.168.1.168/24 gw4 192.168.1.2
$ sudo nmcli con mod 'static-fedora' ipv4.dns 192.168.1.2
$ sudo nmcli con up 'static-fedora'
$ ip a s enp0s3
```


## 2. 软件安装

### 2.1. jdk1.8
1.使用FinalShell 上传JDK 到Linux虚拟机的/root目录下

2.解压JDK压缩包：  
	命令：tar -zxvf /root/jdk-8u351-linux-x64.tar.gz -C /opt

3.修改解压目录名称 mv 或 ln -s <源目录名> <目标目录名>
	命令：mv /opt/jdk1.8.0_351/ /opt/jdk/

4.设置启动环境变量 修改配置文件 /root/.bashrc 
	1)使用FinalShell下载 /root/.bashrc文件到Windows主机上
	2)修改配置文件内容
	3)上传修改后的.bashrc文件到Linux虚拟机中/root目录中
	【命令】vim /root/.bashrc

配置内容
	export JAVA_HOME=/opt/jdk
	export PATH=$PATH:$JAVA_HOME/bin

5.使配置生效 或 退出系统( 命令 exit )重新登录
	【命令】source /root/.bashrc

6.验证 JDK 安装是否成功 ，在任意目录执行 java -version 命令 正显示安装java版本信息
	【命令】java -version



