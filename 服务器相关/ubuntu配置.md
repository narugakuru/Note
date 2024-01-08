---
tags:
  - 网络
  - linux操作
---

# 网络配置
net0桥接
net1仅主机
net8使用net模式，开启DHCP
虚拟机ip为192.168.10.3，网关为192.168.10.2
宿主机ip为192.168.10.1，网关为192.168.10.2


[Vmware虚拟机Linux配置固定IP地址（详细版）_虚拟机固定ip_jsryin的博客-CSDN博客](https://blog.csdn.net/jsryin/article/details/123304582)

# 配置SSH

apt-get install ssh
sudo /etc/init.d/ssh start

修改SSH配置文件

**可以通过SSH配置文件更改包括端口、是否允许root登录等设置，配置文件位置： /etc/ssh/sshd_config。**

```
打开配置文件
sudo vim /etc/ssh/sshd_config

1)默认是不允许root远程登录的，可以通过配置文件修改允许root远程登录参数。

找到 PermitRootLogin without-password 
修改为 
PermitRootLogin yes

2）默认不允许ssh密码验证，需将 PasswordAuthentication no 改为  PasswordAuthentication yes。

# 重启SSH服务
/etc/init.d/ssh restart

```

# 换源
```shell
cp /etc/apt/sources.list /etc/apt/sources.list.backup
nano /etc/apt/sources.list
```

vim
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse    
```

# LVM分区

要使用 LVM 创建和调整分区，您可以按照以下步骤进行操作：

1. 准备磁盘：首先，确保您有可用的磁盘或分区来创建物理卷。您可以使用工具如 `fdisk` 或 `parted` 来创建分区。

2. 创建物理卷：使用 `pvcreate` 命令创建物理卷。例如，如果要将 `/dev/sdb1` 分区创建为物理卷，可以运行以下命令：
   ```
   sudo pvcreate /dev/sdb1
   ```

3. 创建卷组：使用 `vgcreate` 命令创建卷组，并将一个或多个物理卷添加到卷组中。例如，创建一个名为 `myvg` 的卷组，并将 `/dev/sdb1` 添加到该卷组中，可以运行以下命令：
   ```
   sudo vgcreate myvg /dev/sdb1
   ```

4. 创建逻辑卷：使用 `lvcreate` 命令在卷组中创建逻辑卷。指定逻辑卷的名称、大小和所属的卷组。例如，创建一个名为 `mylv` 的逻辑卷，大小为 10GB，属于 `myvg` 卷组，可以运行以下命令：
   ```
   sudo lvcreate -L 10G -n mylv myvg
   ```

5. 格式化逻辑卷：使用适当的文件系统格式化逻辑卷。例如，如果要使用 ext4 文件系统格式化 `mylv` 逻辑卷，可以运行以下命令：
   ```
   sudo mkfs.ext4 /dev/myvg/mylv
   ```

6. 挂载逻辑卷：创建一个目录作为挂载点，并将逻辑卷挂载到该目录。例如，创建一个名为 `/mnt/mylv` 的目录，并将 `mylv` 逻辑卷挂载到该目录，可以运行以下命令：
   ```
   sudo mkdir /mnt/mylv
   sudo mount /dev/myvg/mylv /mnt/mylv
   ```

现在，您已成功创建了一个 LVM 逻辑卷，并将其挂载到指定目录。您可以在 `/mnt/mylv` 目录中访问和使用该逻辑卷。

如果您需要调整逻辑卷的大小，可以使用 `lvresize` 命令来增加或减少逻辑卷的大小。请注意，在调整逻辑卷大小之前，确保已备份重要数据，并小心操作以避免数据丢失。

这些是使用 LVM 创建和调整分区的基本步骤。LVM 提供了更多高级功能，如快照、镜像和迁移等，以满足不同的存储管理需求。您可以参考 LVM 的文档和手册来了解更多详细信息和操作示例。

# Swap分配文件

要创建一个8GB大小的Swap分区，请按照以下步骤进行操作：

1. 首先，打开终端（Terminal）。
2. 运行以下命令以创建一个8GB大小的Swap分区：
   ```
   sudo fallocate -l 8G /swapfile
   ```
   这将在根目录下创建一个名为`swapfile`的8GB文件，用作Swap分区。

3. 设置Swap文件的权限，使其只能被root用户读写：
   ```
   sudo chmod 600 /swapfile
   ```

4. 使用以下命令将文件格式化为Swap分区：
   ```
   sudo mkswap /swapfile
   ```

5. 启用新的Swap分区：
   ```
   sudo swapon /swapfile
   ```

6. 最后，您可以编辑`/etc/fstab`文件，以便在系统启动时自动挂载Swap分区。使用文本编辑器打开`/etc/fstab`文件：
   ```
   sudo nano /etc/fstab
   ```

   在文件的末尾添加以下行：
   ```
   /swapfile none swap sw 0 0
   ```

   按下`Ctrl + X`保存并退出编辑器。

现在，您已成功创建了一个8GB大小的Swap分区，并将其配置为在系统启动时自动挂载。您可以使用以下命令验证Swap分区的大小：
```
sudo swapon --show
```

它应该显示8GB的Swap分区。