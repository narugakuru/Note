8'/7L,MNR# 常用工具命令

# 1. ‌Termux小程序‌you-get 

安装you-get

第一步：更新安装器
pkg update && pkg upgrade

第二步：获取sd卡读写权限
termux-setup-storage

第三步：安装python和ffmpeg
pkg install python ffmpeg

第四步：安装you-get
pip install you-get


# 2. 查找linux软件安装文件夹

例如想知道redis安装在哪了, redis的服务端是"redis-server" 
实验环境centos 7.6


方法一:  locate 

1.安装mlocate

yum install mlocate

2.更新数据库

updatedb

3.查找

locate redis-server

/usr/local/bin/ 就是redis的安装位置

方法二: whereis 只显示可执行文件
whereis redis-server

方法三: which只显示文件
which redis-server

方法四: find  文件和文件夹
find / -name redis-server



# 3. Linux系统分类

**一般来说linux系统基本上分两大类：**

RedHat系列：Redhat、Centos、Fedora等
Debian系列：Debian、Ubuntu等

**RedHat 系列**

- 常见的安装包格式 rpm包,安装rpm包的命令是“rpm -参数”
- 包管理工具 yum
- 支持tar包

**Debian系列**

- 常见的安装包格式 deb包,安装deb包的命令是“dpkg -参数”
- 包管理工具 apt-get
- 支持tar包

*tar 只是一种压缩文件格式，它只是把文件压缩打包而已。 rpm 相当于windows中的安装文件，它会自动处理软件包之间的依赖关系。
优缺点来说，rpm一般都是预先编译好的文件，它可能已经绑定到某种CPU或者发行版上面了。
tar一般包括编译脚本，你可以在你的环境下编译，所以具有通用性。
如果你的包不想开放源代码，你可以制作成rpm，如果开源，用tar更方便了。
tar一般都是源码打包的软件，需要自己解包，然后进行安装三部曲，./configure, make, make install. 来安装软件。
安装rpm包的命令是“rpm -参数”，安装deb包的命令是“dpkg -参数”。而linux系统很方便和人性化的一点就是很多软件或服务根本就不用我们去下载，直接使用相应的命令就可以管理了，可能这就是传说中的 “云”的概念。*







# 4. wget命令行下载

下载hadoop

wget https://mirrors.cnnic.cn/apache/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz



# 5. curl命令

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

## 5.1. 文件下载

- curl命令可以用来执行下载、发送各种HTTP请求，指定HTTP头部等操作
- curl是将下载文件输出到stdout，将进度信息输出到stderr，不显示进度信息使用--silent选项。

`--progress`显示进度条

```
curl [网址]
```

## 5.2. 断点续传

```shell
> curl -O -u 'rumenz':'test' ftp://rumenz.com/jdk.tar.gz
```

> 然后你的连接突然断开，你可以用以下命令继续下载

```shell
> curl -C -  -O  -u 'rumenz':'test' ftp://rumenz.com/jdk.tar.gz
```

> 注意断点续传的参数是`-C`, 要自动续传的话要使用 `-C -`, 否则需要手工指定断点的字节位置.



## 5.3. curl指定目录



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



# 6. 指定环境alternatives命令详解
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

说明：
alternatives --install <link> <name> <path> <priority>
其中，
install表示安装
link是符号链接
name则是标识符
path是执行文件的路径
priority则表示优先级




问题现象
