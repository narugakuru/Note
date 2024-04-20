---
tags:
  - Hadoop
---
HADOOP hdfs使用方式
1)Web浏览器
http://<name_node_ip_or_host>:<port>/

2)命令行【fs shell】
调用文件系统(FS)Shell命令应使用 bin/hadoop fs <args>的形式。 所有的的FS shell命令使用URI路径作为参数。URI格式是scheme://authority/path。对HDFS文件系统，scheme是hdfs，对本地文件系统，scheme是file。

$HADOOP_HOME/bin/hadoop fs <args>
【命令参考手册】 https://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html#FS+Shell

【可选】hadoop命令加入系统环境变量PATH中
	vim ~/.bashrc
	输入 export PATH=$PATH:$HADOOP_HOME/bin
	保存退出
	source ~/.bashrc

3)RPC接口 9000 [Binary]
	a)编程连接
		Java开发
	b)第三工具
		IDEA安装[Big Data Tools]插件【已安装IDEA，建议同时安装[scala]语言插件】

4）Webhdfs RESTful API 80 [JSON, XML]
	postman测试 【postman下载地址】https://www.postman.com/downloads/

# 概述

文件系统（FS）shell包括各种类似shell的命令，这些命令直接与Hadoop分布式文件系统（HDFS）以及Hadoop支持的其他文件系统（如Local FS，WebHDFS，S3 FS等）交互。FS 外壳由以下人员调用：

bin/hadoop fs <args>

所有 FS 外壳命令都采用路径 URI 作为参数。URI 格式为 scheme://authority/path。对于HDFS，方案是hdfs，对于本地FS，方案是文件。方案和权限是可选的。如果未指定，则使用配置中指定的默认方案。HDFS 文件或目录（如 /parent/child）可以指定为 hdfs://namenodehost/parent/child 或简单地指定为 /parent/child（假设您的配置设置为指向 hdfs://namenodehost）。

FS shell 中的大多数命令的行为类似于相应的 Unix 命令。每个命令都描述了差异。错误信息被发送到stderr，输出被发送到stdout。

如果使用HDFS，hdfs dfs是一个同义词。

可以使用相对路径。对于HDFS，当前工作目录是HDFS主目录/user/<username>通常必须手动创建。HDFS 主目录也可以隐式访问，例如，当使用 HDFS 垃圾文件夹时，.主目录中的废纸篓目录。

有关通用外壳选项，请参阅[命令手册](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)。

## 追加到文件

用法： hadoop fs -appendToFile <localsrc> ...<夏令时>

将单个 src 或多个 src 从本地文件系统追加到目标文件系统。还从 stdin 读取输入并附加到目标文件系统。

- hadoop fs -appendToFile localfile /user/hadoop/hadoopfile
- hadoop fs -appendToFile localfile1 localfile2 /user/hadoop/hadoopfile
- hadoop fs -appendToFile localfile hdfs://nn.example.com/hadoop/hadoopfile
- Hadoop fs -appendToFile - hdfs://nn.example.com/hadoop/hadoopfile从标准输入读取。

退出代码：

成功时返回 0，错误时返回 1。

## 猫

用法： hadoop fs -cat [-ignoreCrc] URI [URI ...]

将源路径复制到标准输出。

选项

- -ignoreCrc 选项禁用检查验证。

例：

- 哈多普 FS -猫 hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2
- Hadoop FS -cat file:///file3 /user/hadoop/file4

退出代码：

成功时返回 0，错误时返回 -1。

## 校验和

用法： hadoop fs -checksum [-v] URI

返回文件的校验和信息。

选项

- -v 选项显示文件的块大小。

例：

- Hadoop FS -校验和 hdfs://nn1.example.com/file1
- Hadoop FS - 校验和 file:///etc/hosts

## 中国人权和地质公园

用法： hadoop fs -chgrp [-R] GROUP URI [URI ...]

更改文件的组关联。用户必须是文件的所有者，或者是超级用户。其他信息在[权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)中。

选项

- -R 选项将通过目录结构递归进行更改。

## CHMOD

用法： hadoop fs -chmod [-R] <MODE[，MODE]... |八进制模式> URI [URI ...]

更改文件的权限。使用 -R，通过目录结构以递归方式进行更改。用户必须是文件的所有者，或者是超级用户。其他信息在[权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)中。

选项

- -R 选项将通过目录结构递归进行更改。

## 乔恩

用法： hadoop fs -chown [-R] [OWNER][：[GROUP]] URI [URI ]

更改文件的所有者。用户必须是超级用户。其他信息在[权限指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)中。

选项

- -R 选项将通过目录结构递归进行更改。

## 复制自本地

与 -put 命令相同。

## 复制到本地

与 -get 命令相同。

## 计数

用法： hadoop fs -count [-q] [-h] [-v] [-x] [-t [<存储类型>]] [-u] [-e] [-s] <路径>

计算与指定文件模式匹配的路径下的目录、文件和字节数。获取配额和使用情况。带有 -count 的输出列包括：DIR_COUNT、FILE_COUNT、CONTENT_SIZE、路径名

-u 和 -q 选项控制输出包含哪些列。-q 表示显示配额，-u 将输出限制为仅显示配额和使用情况。

带有 -count -q 的输出列是：配额、REMAINING_QUOTA、SPACE_QUOTA、REMAINING_SPACE_QUOTA、DIR_COUNT、FILE_COUNT、CONTENT_SIZE、路径名

带有 -count -u 的输出列是：配额、REMAINING_QUOTA、SPACE_QUOTA、REMAINING_SPACE_QUOTA、路径名

-t 选项显示每种存储类型的配额和使用情况。如果未给出 -u 或 -q 选项，则忽略 -t 选项。可以在 -t 选项中使用的可能参数列表（除参数 “” 外不区分大小写）：“、”全部“、”ram_disk“、”ssd“、”磁盘“或”存档”。

-h 选项以人类可读的格式显示大小。

-v 选项显示标题行。

-x 选项从结果计算中排除快照。如果没有 -x 选项（缺省值），则始终根据所有 INodes 计算结果，包括给定路径下的所有快照。如果给定了 -u 或 -q 选项，则忽略 -x 选项。

-e 选项显示每个文件的纠删码策略。

带有 -count -e 的输出列是：DIR_COUNT、FILE_COUNT、CONTENT_SIZE、ERASURECODING_POLICY、路径名

ERASURECODING_POLICY是文件策略的名称。如果在该文件上设置了纠删码策略，它将返回策略的名称。如果未设置纠删码策略，它将返回“已复制”，这意味着它使用复制存储策略。

-s 选项显示每个目录的快照计数。

例：

- Hadoop FS -count hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2
- Hadoop FS -count -q hdfs://nn1.example.com/file1
- Hadoop fs -count -q -h hdfs://nn1.example.com/file1
- Hadoop fs -count -q -h -v hdfs://nn1.example.com/file1
- Hadoop FS -count -u hdfs://nn1.example.com/file1
- Hadoop FS -count -u -h hdfs://nn1.example.com/file1
- Hadoop FS -count -u -h -v hdfs://nn1.example.com/file1
- Hadoop FS -count -e hdfs://nn1.example.com/file1
- Hadoop FS -count -s hdfs://nn1.example.com/file1

退出代码：

成功时返回 0，错误时返回 -1。

## 正轨

用法： hadoop fs -cp [-f] [-p | -p[topax]] [-t <线程计数>] [-q <线程池队列大小>] URI [URI ...] <dest>

将文件从源复制到目标。此命令还允许多个源，在这种情况下，目标必须是目录。

如果 （1） 源和目标文件系统支持“raw.*”命名空间扩展属性（仅限 HDFS），并且 （2） 所有源和目标路径名都在 /.reserved/raw 层次结构中，则会保留这些属性。是否保留原始 xattr 的确定与 -p（保留）标志无关。

选项：

- -f ：覆盖目标（如果已存在）。
- -d ：跳过创建带有后缀._COPYING_的临时文件。
- -p ：保留文件属性 [topx]（时间戳、所有权、权限、ACL、XAttr）。如果指定 -p 时没有 _arg_，则保留时间戳、所有权和权限。如果指定了 -pa，则保留权限，因为 ACL 是权限的超集。是否保留原始命名空间扩展属性的确定与 -p 标志无关。
- -t <线程计数>：要使用的线程数，默认值为 1。复制包含多个文件的目录时很有用。
- -q <线程池队列大小>：要使用的线程池队列大小，默认值为 1024。仅当线程计数大于 1 时，它才会生效。

例：

- Hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2
- Hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir
- Hadoop FS -cp -t 5 /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir
- Hadoop FS -cp -t 10 -q 2048 /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir

退出代码：

成功时返回 0，错误时返回 -1。

## 创建快照

请参阅 [HDFS 快照指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html)。

## 删除快照

请参阅 [HDFS 快照指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html)。

## 东风

用法： hadoop fs -df [-h] URI [URI ...]

显示可用空间。

选项：

- -h 选项将以“人类可读”的方式格式化文件大小（例如 64.0m 而不是 67108864）

例：

- Hadoop DFS -df /user/hadoop/dir1

## 都

用法： hadoop fs -du [-s] [-h] [-v] [-x] URI [URI ...]

显示给定目录中包含的文件和目录的大小，或者文件的长度（如果它只是一个文件）。

选项：

- -s 选项将导致显示文件长度的聚合摘要，而不是单个文件。如果没有 -s 选项，则通过从给定路径深入 1 级来完成计算。
- -h 选项将以“人类可读”的方式格式化文件大小（例如 64.0m 而不是 67108864）
- -v 选项会将列的名称显示为标题行。
- -x 选项将从结果计算中排除快照。如果没有 -x 选项（缺省值），则始终根据所有 INodes 计算结果，包括给定路径下的所有快照。

du 返回三列，格式如下：

size disk_space_consumed_with_all_replicas full_path_name

例：

- Hadoop fs -du /user/hadoop/dir1 /user/hadoop/file1 hdfs://nn.example.com/user/hadoop/dir1

退出代码：成功时返回 0，出错时返回 -1。

## 杜斯

用法： hadoop fs -dus <args>

显示文件长度的摘要。

**注意：**此命令已弃用。而是使用 hadoop fs -du -s。

## 清除

用法： hadoop fs -expunge [-imimmediate ] [-fs <path>]

从垃圾箱目录中永久删除早于保留阈值的检查点中的文件，并创建新的检查点。

创建检查点时，回收站中最近删除的文件将移动到检查点下。早于 fs.trash.interval 的检查点中的文件将在下次调用 -expunge 命令时永久删除。

如果文件系统支持该功能，用户可以配置为通过存储为 fs.trash.checkpoint.interval 的参数（在 core-site.xml 中）定期创建和删除检查点。此值应小于或等于 fs.trash.interval。

如果传递了 -immediate 选项，则会立即删除当前用户的回收站中的所有文件，忽略 fs.trash.interval 设置。

如果传递了 -fs 选项，则提供的文件系统将被清除，而不是创建缺省文件系统和检查点。

例如

hadoop fs -expunge --immediate -fs s3a://landsat-pds/

有关 HDFS 垃圾功能的更多信息，请参阅 [HDFS 架构指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#File_Deletes_and_Undeletes)。

## 找到

用法： hadoop fs -查找<路径> ...<表情>...

查找与指定表达式匹配的所有文件，并对其应用选定的操作。如果未指定_路径_，则默认为当前工作目录。如果未指定表达式，则默认为 -print。

识别以下主要表达式：

- -名称模式-名称模式  
    
    如果文件的基本名称与使用标准文件系统通配的模式匹配，则计算为 true。如果使用 -iname，则匹配不区分大小写。
    
- -打印-打印  
    0
    
    始终计算为 true。使当前路径名写入标准输出。如果使用 -print0 表达式，则会追加 ASCII NULL 字符。
    

以下运营商得到认可：

- 表达式 -表达式表达式 -和表达式表达式  
    表达式  
    
    用于连接两个表达式的逻辑 AND 运算符。如果两个子表达式都返回 true，则返回 true。由两个表达式的并置暗示，因此不需要显式指定。如果第一个表达式失败，则不会应用第二个表达式。
    

例：

Hadoop FS -查找 / -名称测试 -打印

退出代码：

成功时返回 0，错误时返回 -1。

## 获取

用法： hadoop fs -get [-ignorecrc] [-crc] [-p] [-f] [-t <线程计数>] [-q <线程池队列大小>] <src> ...<本地>

将文件复制到本地文件系统。未通过 CRC 检查的文件可以使用 -ignorecrc 选项进行复制。可以使用 -crc 选项复制文件和 CRC。

选项：

- -p ：保留访问和修改时间、所有权和权限。（假设权限可以跨文件系统传播）
- -f ：覆盖目标（如果已存在）。
- -ignorecrc ：跳过对下载文件的 CRC 检查。
- -crc：为下载的文件写入 CRC 校验和。
- -t <线程计数>：要使用的线程数，默认值为 1。下载包含 1 个以上文件的目录时很有用。
- -q <线程池队列大小>：要使用的线程池队列大小，默认值为 1024。仅当线程计数大于 1 时，它才会生效。

例：

- Hadoop FS -get /user/hadoop/file localfile
- Hadoop FS -get hdfs://nn.example.com/user/hadoop/file localfile
- Hadoop FS -get -t 10 hdfs://nn.example.com/user/hadoop/dir1 localdir
- Hadoop FS -get -t 10 -q 2048 hdfs://nn.example.com/user/hadoop/dir* localdir

退出代码：

成功时返回 0，错误时返回 -1。

## 格特法克

用法： hadoop fs -getfacl [-R] <path>

显示文件和目录的访问控制列表 （ACL）。如果目录具有默认 ACL，则 getfacl 也会显示默认 ACL。

选项：

- -R：递归列出所有文件和目录的 ACL。
- _路径_：要列出的文件或目录。

例子：

- Hadoop FS -getfacl /file
- Hadoop fs -getfacl -R /dir

退出代码：

成功时返回 0，出错时返回非零。

## 盖法特尔

用法： hadoop fs -getfattr [-R] -n name | -d [-e en] <path>

显示文件或目录的扩展属性名称和值（如果有）。

选项：

- -R：递归列出所有文件和目录的属性。
- -n 名称：转储命名的扩展属性值。
- -d：转储与路径名关联的所有扩展属性值。
- -e _编码_：在检索值后对其进行编码。有效的编码是“文本”、“十六进制”和“base64”。编码为文本字符串的值括在双引号 （“） 中，编码为十六进制和 base64 的值分别以 0x 和 0 为前缀。
- _路径_：文件或目录。

例子：

- Hadoop FS -getfattr -d /file
- hadoop fs -getfattr -R -n user.myAttr /dir

退出代码：

成功时返回 0，出错时返回非零。

## 获取合并

用法： hadoop fs -getmerge [-nl] <src> <localdst>

将源目录和目标文件作为输入，并将 src 中的文件连接到目标本地文件中。（可选）可以设置 -nl 以允许在每个文件的末尾添加换行符 （LF）。-skip-empty-file 可用于避免在空文件的情况下使用不需要的换行符。

例子：

- Hadoop FS -getmerge -nl /src /opt/output.txt
- Hadoop fs -getmerge -nl /src/file1.txt /src/file2.txt /output.txt

退出代码：

成功时返回 0，出错时返回非零。

## 头

用法： hadoop fs -head URI

显示要标准输出的文件的第一千字节。

例：

- Hadoop FS -head 路径名

退出代码：成功时返回 0，出错时返回 -1。

## 帮助

用法： 哈多普 fs -帮助

返回使用情况输出。

## ls

用法： hadoop fs -ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] <args>

选项：

- -C：仅显示文件和目录的路径。
- -d：目录作为纯文件列出。
- -h：以人类可读的方式格式化文件大小（例如 64.0m 而不是 67108864）。
- -问：打印？而不是不可打印的字符。
- -R：递归列出遇到的子目录。
- -t：按修改时间对输出进行排序（最近的在前）。
- -S：按文件大小对输出进行排序。
- -r：反转排序顺序。
- -u：使用访问时间而不是修改时间进行显示和排序。
- -e：仅显示文件和目录的纠删码策略。

对于文件，ls 返回文件的统计信息，格式如下：

permissions number_of_replicas userid groupid filesize modification_date modification_time filename

对于一个目录，它返回其直接子级的列表，就像在 Unix 中一样。目录列为：

permissions userid groupid modification_date modification_time dirname

默认情况下，目录中的文件按文件名排序。

例：

- Hadoop fs -ls /user/hadoop/file1
- Hadoop fs -ls -e /ecdir

退出代码：

成功时返回 0，错误时返回 -1。

## LSR

用法： hadoop fs -lsr <args>

ls 的递归版本。

**注意：**此命令已弃用。而是使用 hadoop fs -ls -R

## 姆克迪尔

用法： hadoop fs -mkdir [-p] <paths>

将路径 uri 作为参数并创建目录。

选项：

- -p 选项行为很像 Unix mkdir -p，沿路径创建父目录。

例：

- Hadoop fs -mkdir /user/hadoop/dir1 /user/hadoop/dir2
- Hadoop FS -MKDIR hdfs://nn1.example.com/user/hadoop/dir hdfs://nn2.example.com/user/hadoop/dir

退出代码：

成功时返回 0，错误时返回 -1。

## 移动从本地

用法： hadoop fs -moveFromLocal <localsrc> <dst>

与 put 命令类似，不同之处在于源 localsrc 在复制后被删除。

## 移动到本地

用法： hadoop fs -moveToLocal [-crc] <src> <dst>

显示“尚未实现”消息。

## 中密度纤维板

用法： hadoop fs -mv URI [URI ...] <dest>

将文件从源移动到目标。此命令还允许多个源，在这种情况下，目标需要是一个目录。不允许跨文件系统移动文件。

例：

- Hadoop fs -mv /user/hadoop/file1 /user/hadoop/file2
- Hadoop FS -MV hdfs://nn.example.com/file1 hdfs://nn.example.com/file2 hdfs://nn.example.com/file3 hdfs://nn.example.com/dir1

退出代码：

成功时返回 0，错误时返回 -1。

## 放

用法： hadoop fs -put [-f] [-p] [-l] [-d] [-t <线程计数>] [-q <线程池队列大小>] [ - | <localsrc> ...] <dst>

将单个 src 或多个 src 从本地文件系统复制到目标文件系统。还从 stdin 读取输入，如果源设置为“-”，则写入目标文件系统

如果文件已存在，则复制将失败，除非给出 -f 标志。

选项：

- -p ：保留访问和修改时间、所有权和权限。（假设权限可以跨文件系统传播）
- -f ：覆盖目标（如果已存在）。
- -l ：允许 DataNode 延迟将文件持久保存到磁盘，强制复制因子为 1。此标志将导致持久性降低。请小心使用。
- -d ：跳过创建带有后缀._COPYING_的临时文件。
- -t <线程计数>：要使用的线程数，默认值为 1。上传包含 1 个以上文件的目录时很有用。
- -q <线程池队列大小>：要使用的线程池队列大小，默认值为 1024。仅当线程计数大于 1 时，它才会生效。

例子：

- Hadoop FS -put localfile /user/hadoop/hadoopfile
- Hadoop fs -put -f localfile1 localfile2 /user/hadoop/hadoopdir
- Hadoop FS -put -d localfile hdfs://nn.example.com/hadoop/hadoopfile
- 哈多普 FS -放 - hdfs://nn.example.com/hadoop/hadoopfile从标准输入读取。
- Hadoop FS -put -t 5 localdir hdfs://nn.example.com/hadoop/hadoopdir
- Hadoop FS -put -t 10 -q 2048 localdir1 localdir2 hdfs://nn.example.com/hadoop/hadoopdir

退出代码：

成功时返回 0，错误时返回 -1。

## 重命名快照

请参阅 [HDFS 快照指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html)。

## .rm

用法： hadoop fs -rm [-f] [-r |-R] [-skipTrash] [-safely] URI [URI ...]

删除指定为 args 的文件。

如果启用了垃圾箱，文件系统会将已删除的文件移动到垃圾目录（由[文件系统#getTrashRoot](https://hadoop.apache.org/docs/current/api/org/apache/hadoop/fs/FileSystem.html)提供）。

目前，垃圾箱功能默认处于禁用状态。用户可以通过为参数fs.trash.interval设置大于零的值来启用垃圾箱（在core-site.xml中）。

请参阅[关于](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge)删除回收站中的文件的清除。

选项：

- 如果文件不存在，-f 选项将不会显示诊断消息或修改退出状态以反映错误。
- -R 选项以递归方式删除目录及其下的任何内容。
- -r 选项等效于 -R。
- -skipTrash 选项将绕过废纸篓（如果启用），并立即删除指定的文件。当需要从超出配额的目录中删除文件时，这可能很有用。
- -safe 选项在删除文件总数大于 hadoop.shell.delete.limit.num.files（在 core-site.xml 中，默认值：100）之前需要安全确认。它可以与 -skipTrash 一起使用，以防止意外删除大型目录。在确认之前以递归方式遍历大目录以计算要删除的文件数时，预计会出现延迟。

例：

- Hadoop FS -RM hdfs://nn.example.com/file /user/Hadoop/emptydir

退出代码：

成功时返回 0，错误时返回 -1。

## 目录

用法： hadoop fs -rmdir [--ignore-fail-on-non-empty] URI [URI ...]

删除目录。

选项：

- --ignore-fail-on-non-empty：使用通配符时，如果目录仍包含文件，则不要失败。

例：

- Hadoop fs -rmdir /user/hadoop/emptydir

## RMR

用法： hadoop fs -rmr [-skipTrash] URI [URI ...]

删除的递归版本。

**注意：**此命令已弃用。改用 hadoop fs -rm -r

## 塞特法克

用法： hadoop fs -setfacl [-R] [-b |-k -m |-x <acl_spec> <path>] |[--设置<acl_spec> <路径>]

设置文件和目录的访问控制列表 （ACL）。

选项：

- -b：删除除基本 ACL 条目之外的所有条目。保留用户、组和其他条目，以便与权限位兼容。
- -k：删除默认 ACL。
- -R：递归地将操作应用于所有文件和目录。
- -m：修改 ACL。新条目将添加到 ACL，并保留现有条目。
- -x：删除指定的 ACL 条目。将保留其他 ACL 条目。
- --set：完全替换 ACL，丢弃所有现有条目。_acl_spec_必须包含用户、组和其他条目，以便与权限位兼容。如果 ACL 规范仅包含访问条目，则会保留现有的缺省条目。如果 ACL 规范仅包含缺省条目，则会保留现有访问条目。如果 ACL 规范同时包含访问条目和默认条目，则两者都会被替换。
- _acl_spec_：以逗号分隔的 ACL 条目列表。
- _路径_：要修改的文件或目录。

例子：

- Hadoop fs -setfacl -m User：Hadoop：RW- /file
- Hadoop FS -setfacl -x 用户：Hadoop /file
- Hadoop FS -setfacl -b /file
- Hadoop FS -setfacl -k /dir
- Hadoop fs -setfacl --set user：：rw-，user：hadoop：rw-，group：：r--,other：：r-- /file
- Hadoop fs -setfacl -R -m User：Hadoop：R-X /Dir
- Hadoop fs -setfacl -m default：User：Hadoop：R-X /Dir

退出代码：

成功时返回 0，出错时返回非零。

## 塞法特尔

用法： hadoop fs -setfattr -n name [-v value] | -x name <path>

设置文件或目录的扩展属性名称和值。

选项：

- -n 名称：扩展属性名称。
- -v 值：扩展属性值。该值有三种不同的编码方法。如果参数括在双引号中，则值是引号内的字符串。如果参数以 0x 或 0X 为前缀，则将其视为十六进制数。如果参数以 0 或 0S 开头，则将其视为 base64 编码。
- -x 名称：删除扩展属性。
- _路径_：文件或目录。

例子：

- hadoop fs -setfattr -n user.myAttr -v myValue /file
- Hadoop fs -setfattr -n user.noValue /file
- hadoop fs -setfattr -x user.myAttr /file

退出代码：

成功时返回 0，出错时返回非零。

## 塞特雷普

用法： hadoop fs -setrep [-R] [-w] <numReplicas> <path>

更改文件的复制因子。如果 _path_ 是目录，则该命令会递归更改目录树根植于 _path_ 下所有文件的复制因子。执行此命令时将忽略 EC 文件。

选项：

- -w 标志请求命令等待复制完成。这可能需要很长时间。
- 接受 -R 标志以实现向后兼容性。它没有效果。

例：

- Hadoop FS -setrep -w 3 /user/hadoop/dir1

退出代码：

成功时返回 0，错误时返回 -1。

## 统计

用法： hadoop fs -stat [格式] <路径> ...

以指定格式在 <path> 处打印有关文件/目录的统计信息。格式接受八进制 （%a） 和符号 （%A） 的权限，文件大小（以字节为单位 （%b）、类型 （%F）、所有者的组名 （%g）、名称 （%n）、块大小 （%o）、复制 （%r）、所有者的用户名 （%u）、访问日期 （%x， %X） 和修改日期 （%y， %Y）。%x 和 %y 将 UTC 日期显示为“yyyy-MM-dd HH：mm：ss”，%X 和 %Y 显示自 1 年 1970 月 <> 日 UTC 以来的毫秒数。如果未指定格式，则默认使用 %y。

例：

- Hadoop fs -stat “type：%F perm：%a %u：%g size：%b mtime：%y atime：%x name：%n” /file

退出代码：成功时返回 0，出错时返回 -1。

## 尾巴

用法： hadoop fs -tail [-f] URI

显示文件的最后一千字节到标准输出。

选项：

- -f 选项将随着文件的增长输出附加的数据，就像在 Unix 中一样。

例：

- Hadoop FS -tail pathname

退出代码：成功时返回 0，出错时返回 -1。

## 测试

用法： hadoop fs -test -[defswrz] URI

选项：

- -d：如果路径是目录，则返回 0。
- -e：如果路径存在，则返回 0。
- -f：如果路径是文件，则返回 0。
- -s：如果路径不为空，则返回 0。
- -w：如果路径存在且授予了写入权限，则返回 0。
- -r：如果路径存在且授予读取权限，则返回 0。
- -z：如果文件长度为零，则返回 0。

例：

- Hadoop FS -test -e 文件名

## 发短信

用法： hadoop fs -text <src>

获取源文件并以文本格式输出文件。允许的格式是 zip 和 TextRecordInputStream。

## 触摸

用法： hadoop fs -touch [-a] [-m] [-t 时间戳] [-c] URI [URI ...]

将 URI 指定的文件的访问和修改时间更新为当前时间。如果该文件不存在，则会在 URI 上创建一个长度为零的文件，并将当前时间作为该 URI 的时间戳。

- 使用 -a 选项仅更改访问时间
- 使用 -m 选项仅更改修改时间
- 使用 -t 选项指定时间戳（格式为 yyyyMMdd：HHmmss）而不是当前时间
- 如果文件不存在，则使用 -c 选项不创建文件

时间戳格式如下 * yyyy 四位数年份（例如 2018 年）* MM 一年中的两位数月份（例如 08 表示 01 月）* dd 月份中的两位数日期（例如 24 表示该月的第一天） * HH 使用 23 小时表示法的一天中的两位数小时（例如 11 代表晚上 11 点， 11 代表上午 20180809 点） * mm 小时中的两位数分钟 * ss 分钟的两位数秒，例如 230000：9 代表 2018 年 11 月 <> 日晚上 <> 点

例：

- Hadoop FS -触摸路径名
- Hadoop fs -touch -m -t 20180809：230000 路径名
- Hadoop FS -touch -t 20180809：230000 路径名
- Hadoop FS -触摸 -A 路径名

退出代码：成功时返回 0，出错时返回 -1。

## 触摸

用法： hadoop fs -touchz URI [URI ...]

创建零长度的文件。如果文件存在非零长度，则返回错误。

例：

- Hadoop FS -Touchz 路径名

退出代码：成功时返回 0，出错时返回 -1。

## 截断

用法： hadoop fs -truncate [-w] <length> <paths>

将与指定文件模式匹配的所有文件截断为指定长度。

选项：

- 如有必要，-w 标志请求命令等待块恢复完成。  
    如果没有 -w 标志，则在恢复过程中，文件可能会在一段时间内保持未关闭状态。  
    在此期间，无法重新打开文件进行追加。

例：

- Hadoop FS -truncate 55 /user/hadoop/file1 /user/hadoop/file2
- Hadoop FS -截断 -W 127 hdfs://nn1.example.com/user/hadoop/file1

## 康卡特

用法：hadoop fs -concat <目标文件> <源文件>

将现有源文件连接到目标文件中。目标文件和源文件应位于同一目录中。

例：

- Hadoop FS -Concat hdfs://cluster/user/hadoop/target-file hdfs://cluster/user/hadoop/file-0 hdfs://cluster/user/hadoop/file-1

## 用法

用法：hadoop fs -usage 命令

返回单个命令的帮助。

# 使用对象存储

Hadoop FileSystem shell 适用于对象存储，如 Amazon S3、Azure WASB 和 OpenStack Swift。

# Create a directory

hadoop fs -mkdir s3a://bucket/datasets/

# Upload a file from the cluster filesystem

hadoop fs -put /datasets/example.orc s3a://bucket/datasets/

# touch a file

hadoop fs -touchz wasb://yourcontainer@youraccount.blob.core.windows.net/touched

与普通文件系统不同，重命名对象存储中的文件和目录通常需要与所操作对象的大小成正比的时间。由于许多文件系统 shell 操作使用重命名作为操作的最后阶段，因此跳过该阶段可以避免长时间的延迟。

特别是，put 和 copyFromLocal 命令都应该为直接上载设置 -d 选项。

# Upload a file from the cluster filesystem

hadoop fs -put -d /datasets/example.orc s3a://bucket/datasets/

# Upload a file from under the user's home directory in the local filesystem

# Note it is the shell expanding the "~", not the hadoop fs command

hadoop fs -copyFromLocal -d -f ~/datasets/devices.orc s3a://bucket/datasets/

# create a file from stdin

# the special "-" source means "use stdin"

echo "hello" | hadoop fs -put -d -f - wasb://yourcontainer@youraccount.blob.core.windows.net/hello.txt

可以下载和查看对象：

# copy a directory to the local filesystem

hadoop fs -copyToLocal s3a://bucket/datasets/

# copy a file from the object store to the cluster filesystem

hadoop fs -get wasb://yourcontainer@youraccount.blob.core.windows.net/hello.txt /examples

# print the object

hadoop fs -cat wasb://yourcontainer@youraccount.blob.core.windows.net/hello.txt

# print the object, unzipping it if necessary

hadoop fs -text wasb://yourcontainer@youraccount.blob.core.windows.net/hello.txt

## download log files into a local file

hadoop fs -getmerge wasb://yourcontainer@youraccount.blob.core.windows.net/logs\* log.txt

列出许多文件的命令往往比使用 HDFS 或其他文件系统时慢得多

hadoop fs -count s3a://bucket/
hadoop fs -du s3a://bucket/

其他慢速命令包括find，mv，cp和rm。

**找到**

在提供的路径下有许多目录的大型存储中，这可能非常慢。

# enumerate all files in the object store's container

hadoop fs -find s3a://bucket/ -print

# remember to escape the wildcards to stop the shell trying to expand them first

hadoop fs -find s3a://bucket/datasets/ -name \*.txt -print

**重命名**

重命名文件的时间取决于其大小。

重命名目录的时间取决于该目录下所有文件的数量和大小。

hadoop fs -mv s3a://bucket/datasets s3a://bucket/historical

如果操作中断，对象存储将处于未定义状态。

**复制**

hadoop fs -cp s3a://bucket/datasets s3a://bucket/historical

复制操作读取每个文件，然后将其写回对象存储;完成时间取决于要复制的数据量，以及本地计算机和对象存储之间的双向带宽。

**计算机离对象存储越远，复制所需的时间就越长**

## Deleting objects

The rm command will delete objects and directories full of objects. If the object store is _eventually consistent_, fs ls commands and other accessors may briefly return the details of the now-deleted objects; this is an artifact of object stores which cannot be avoided.

If the filesystem client is configured to copy files to a trash directory, this will be in the bucket; the rm operation will then take time proportional to the size of the data. Furthermore, the deleted files will continue to incur storage costs.

若要避免这种情况，请使用 -skipTrash 选项。

hadoop fs -rm -skipTrash s3a://bucket/dataset

数据移动到 .可以使用清除命令清除废纸篓目录。由于此命令仅适用于默认文件系统，因此必须将其配置为使默认文件系统成为目标对象存储。

hadoop fs -expunge -D fs.defaultFS=s3a://bucket/

## 覆盖对象

如果对象存储是_最终一致的_，则覆盖现有对象的任何操作可能不会立即对所有客户端/查询可见。也就是说：后面查询同一对象状态或内容的操作可能会得到前一个对象。这有时会在读取单个对象时出现在同一个客户端中。

避免使用一系列命令覆盖对象，然后立即处理更新的数据;存在改用先前数据的风险。

## 时间 戳

对象存储中对象和目录的时间戳可能不遵循 HDFS 中文件和目录的行为。

1. 对象的创建和初始修改时间将是它在对象存储中创建的时间;这将在写入过程结束时，而不是开始。
2. 时间戳将取自对象存储基础架构的时钟，而不是客户端的时钟。
3. 如果对象被覆盖，修改时间将更新。
4. 目录可能有也可能没有有效的时间戳。当下面的对象更新时，它们不太可能更新其修改时间。
5. Apache Hadoop代码库中的任何对象存储都不支持atime访问时间功能。

请参阅 DistCp 文档，详细了解这可能如何影响 distcp -update 操作。

## 安全模型和操作

对象存储的安全性和权限模型通常与 Unix 样式文件系统的安全和权限模型有很大不同;通常不支持查询或操作权限的操作。

这适用的操作包括：chgrp，chmod，chown，getfacl和setfacl。 相关的属性命令 getfattr 和 setfattr 通常也不可用。

- 列出权限和用户/组详细信息的文件系统命令通常模拟这些详细信息。
    
- 因此，尝试保留权限的操作（例如 fs -put -p）不会保留权限。（特殊情况：wasb://，保留权限但不强制执行权限）。
    

与只读对象存储交互时，在“list”和“stat”命令中找到的权限可能指示用户具有写入访问权限，而实际上他们没有。

对象存储通常有自己的权限模型，模型可以通过特定于存储的工具进行操作。请注意，对象存储可能提供的某些权限（例如只写路径或根路径上的不同权限）可能与 Hadoop 文件系统客户端不兼容。这些往往需要对写入数据的整个对象存储存储桶/容器具有完全读写访问权限。

作为如何模拟权限的示例，以下是 Amazon 的 Landsat 图像的公共只读存储桶的列表：

$ hadoop fs -ls s3a://landsat-pds/
Found 10 items
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/L8
-rw-rw-rw- 1 mapred 23764 2015-01-28 18:13 s3a://landsat-pds/index.html
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/landsat-pds_stats
-rw-rw-rw- 1 mapred 105 2016-08-19 18:12 s3a://landsat-pds/robots.txt
-rw-rw-rw- 1 mapred 38 2016-09-26 12:16 s3a://landsat-pds/run_info.json
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/runs
-rw-rw-rw- 1 mapred 27458808 2016-09-26 12:16 s3a://landsat-pds/scene_list.gz
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/tarq
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/tarq_corrupt
drwxrwxrwx - mapred 0 2016-09-26 12:16 s3a://landsat-pds/test

1. 所有文件都列为具有完全读/写权限。
2. 所有目录似乎都具有完全的 rwx 权限。
3. 所有文件的复制计数为“1”。
4. 所有文件和目录的所有者都声明为当前用户（映射）。
5. 所有目录的时间戳实际上是执行 -ls 操作时的时间戳。这是因为这些目录不是存储中的实际对象;它们是基于其路径下对象的存在的模拟目录。

尝试删除其中一个文件时，操作将失败 — 尽管 ls 命令显示了权限：

$ hadoop fs -rm s3a://landsat-pds/scene_list.gz
rm: s3a://landsat-pds/scene_list.gz: delete on s3a://landsat-pds/scene_list.gz:
  com.amazonaws.services.s3.model.AmazonS3Exception: Access Denied (Service: Amazon S3;
  Status Code: 403; Error Code: AccessDenied; Request ID: 1EF98D5957BCAB3D),
  S3 Extended Request ID: wi3veOXFuFqWBUCJgV3Z+NQVj9gWgZVdXlPU4KBbYMsw/gA+hyhRXcaQ+PogOsDgHh31HlTCebQ=

这表明列出的权限不能作为写入访问权限的证据;只有对象操作才能确定这一点。

请注意，Microsoft Azure WASB 文件系统确实允许设置和检查权限，但实际上并未强制执行权限。此功能提供了使用 DistCP 备份 HDFS 目录树的功能，并保留其权限，这些权限可以在将目录复制回 HDFS 时恢复。但是，为了保护对对象存储中数据的访问，[必须使用 Azure 自己的模型和工具](https://azure.microsoft.com/en-us/documentation/articles/storage-security-guide/)。
