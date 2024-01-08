---
tags:
  - linux操作
  - docker
  - git
---

[[Linux相关]]
# Git使用小坑 Out of memory错误的解决方法

 更新时间：2015年10月19日 13:13:44   作者：UndeadWraith  

这篇文章主要介绍了Git使用小坑 Out of memory错误的解决方法,需要的朋友可以参考下

最近公司将内部使用的代码由svn迁到了git上，所以也必须学者使用Git命令。

  
虽说git的模式和svn区别很大，但想必也不是什么难事。但没曾想在第一步git clone的时候就踩到了一个大坑……废话不多提，先看错误代码：

复制代码代码如下:

  
Cloning into XXXX...  
remote: Couting objects: 125627, done.  
remote: Compressing objects: 100% (47061/47061), done.  
fatal: Out of memory, malloc failed (tried to allocate 1941159936 bytes)  

就这几行错误码，生生的把我给绊住了一天……

**0x00 调内存**

看到“Out of memory, malloc failed”，第一反应是内存不足。毕竟虚拟机内存太小，Debian的虚拟机只给了512M的内存，再加上自己没事鼓捣着玩，自己装了一堆乱七八糟的程序，free的只有几十兆了。

  
于是果断把乱七八糟的进程结束掉，服务停掉，没用的东西全关了。最后又把虚拟机的内存调到了1G.

结果——fatal依然……

**0x01 调配置**

后来又看了下这句——“allocate 1941159936 bytes”——这是1.8G啊……这能有多大内存给他啊……调内存显然不是办法。于是上网搜了一下这个报错，发现都是让调配置的：

复制代码代码如下:

  
git config --global pack.threads 1 git  
config --global pack.deltaCacheSize = 128m  
git config --global pack.windowMemory 50m  

顺便吐槽一句——国内博客全都在抄这个配置……还把Cache抄成了Chase……复制都不会么……

  
这样一来，应该是可以减小资源的占用，但遗憾的是貌似git根本没拾这茬，依然是义无反顾的申请了1.8个G的空间……

当然，结果——再一次的fatal依然……

  
另外，我还找了另一个哥们做了个试验，他的虚拟机里就可以正常clone（1G内存，free不到100M），而我手上的两个虚拟机则都无法正常clone（1G内存，free超过800M 和 2G内存，free将近1.3G）。看起来和内存没什么关系了  

**0x02 调swap**

最后终于看到了这篇：[http://stackoverflow.com/questions/14038074/git-pull-fatal-out-of-memory-malloc-failed](http://stackoverflow.com/questions/14038074/git-pull-fatal-out-of-memory-malloc-failed)

让我眼前一亮的是这个帖子里贴出的错误代码和我的几乎一模一样，而且在一开始就写明了上面的所谓配置方案都已经试过了，但依然无效——和我遇到的情况完全一样。不过他最后的结果是：  

In the end i had to kill old & create new repo.  

貌似是没找到什么好的解决方案……

但当我翻到最下面的时候眼前一亮——“To get around this I temporarily created a large swap drive...”。“a large swap”……醍醐灌顶啊……我立马问了下能正常clone的那个哥们的虚拟机给了多少swap空间，得到的答复是2G，而我手里的——1G和0……和0……和……0……0……0……

原来我需要的是一个大的swap！

虽然我的swap已经是划分好了的，但还是可以添加的，具体方法这篇帖子中也给出了链接：[http://www.thegeekstuff.com/2010/08/how-to-add-swap-space/](http://www.thegeekstuff.com/2010/08/how-to-add-swap-space/)

使用Method2，完美解决。

Method 2: Use a File for Additional Swap Space  
If you don't have any additional disks, you can create a file somewhere on your filesystem, and use that file for swap space.

The following dd command example creates a swap file with the name “myswapfile” under /root directory with a size of 1024MB (1GB).

复制代码代码如下:

  
# dd if=/dev/zero of=/root/myswapfile bs=1M count=1024  
1024+0 records in  
1024+0 records out

# ls -l /root/myswapfile  
-rw-r--r--    1 root     root     1073741824 Aug 14 23:47 /root/myswapfile  

Change the permission of the swap file so that only root can access it.

复制代码代码如下:

  
# chmod 600 /root/myswapfile  

Make this file as a swap file using mkswap command.

复制代码代码如下:

  
# mkswap /root/myswapfile  
Setting up swapspace version 1, size = 1073737 kB  

Enable the newly created swapfile.

复制代码代码如下:

  
# swapon /root/myswapfile  

To make this swap file available as a swap area even after the reboot, add the following line to the /etc/fstab file.

复制代码代码如下:

  
# cat /etc/fstab  
/root/myswapfile               swap                    swap    defaults        0 0  

Verify whether the newly created swap area is available for your use.

复制代码代码如下:

  
# swapon -s  
Filename                        Type            Size    Used    Priority  
/dev/sda2                       partition       4192956 0       -1  
/root/myswapfile                file            1048568 0       -2

# free -k  
             total       used       free     shared    buffers     cached  
Mem:       3082356    3022364      59992          0      52056    2646472  
-/+ buffers/cache:     323836    2758520  
Swap:      5241524          0    5241524  

Note: In the output of swapon -s command, the Type column will say “file” if the swap space is created from a swap file.

If you don't want to reboot to verify whether the system takes all the swap space mentioned in the /etc/fstab, you can do the following, which will disable and enable all the swap partition mentioned in the /etc/fstab

复制代码代码如下:

# swapon -a  

说实在的我一直不太关心swap的大小，总觉得没啥用处。这一次就让我长记性了——swap还是必要的！