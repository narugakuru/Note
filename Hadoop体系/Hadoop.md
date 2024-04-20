---
tags:
  - Hadoop
---

[[hadoop集群]]
[[Hadoop伪分布式部署]]
[[Hive]]
[[Mapreduce]]

# Hadoop安装配置

#Hadoop

1、进入/usr/local目录后，执行解压缩命令：

`tar -zxvf hadoop-2.7.5.tar.gz`
回车后系统开始解压，屏幕会不断滚动解压过程，执行成功后，系统在hadoop目录自动创建hadoop-2.7.5子目录。

2、然后修改文件夹名称为“hadoop”，即hadoop安装目录，执行修改文件夹名称命令：

`mv hadoop-2.7.5 hadoop`

3、进入`/usr/local/hadoop/etc/hadoop`修改`hadoop-env.sh`文件

```shell
 export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
```

在`/etc/profile`和`~/.bashrc`配置环境变量(也可能是~/.bash_profile)

```shell
#HADOOP
export HADOOP_HOME=/usr/local/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

4、配置核心组件`core-site.xml`

```xml
<!--指定集群的文件系统类型：分布式文件系统 -->
<!-- 定义我们文件系统的实现，默认是file:/// 本地文件系统  一定要改掉 hdfs://表示我们的分布式文件存储系统 -->
<property>
    <!-- HDFS的默认路径，端口9000 -->
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value> 
</property>
<property>
    <!-- hadoop运行时，存放数据的临时文件的路径 -->
    <name>hadoop.tmp.dir</name>
    <value>/usr/local/hadoop/hadoopdata</value>
</property>
<!--  缓冲区大小，实际工作中根据服务器性能动态调整 -->
<property>
	<name>io.file.buffer.size</name>
	<value>4096</value>
</property>

<!--  开启hdfs的垃圾桶机制，删除掉的数据可以从垃圾桶中回收，单位分钟 -->
<property>
	<name>fs.trash.interval</name>
	<value>10080</value>
</property>	
```

5、配置文件系统hdfs-site.xml

```xml
<property>
    <!--副本数量，要小于集群主机数量，这里最大可以是3，最小是1-->
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <!--配置节点的存储位置-->
	<name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/tmp/dfs/name</value>
</property>
<property>
	<name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/tmp/dfs/data</value>
</property>
<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>master:50090</value>
</property>
<!-- 定义我们namenode的访问地址,可能通过网页访问这个地址-->
<property>
	<name>dfs.namenode.http-address</name>
	<value>master:50070</value>
</property>
```

6、配置站点文件yarn-site.xml

yarn.resourcemanager.hostname node01

```xml
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>

<!--以下非必需-->
<property>
	<name>yarn.resourcemanager.address</name>
	<value>master:18040</value>
</property>
<property>
	<name>yarn.resourcemanager.scheduler.address</name>
	<value>master:18030</value>
</property>
<property>
	<name>yarn.resourcemanager.resource-tracker.address</name>
	<value>master:18025</value>
</property>
<property>
	<name>yarn.resourcemanager.admin.address</name>
	<value>master:18141</value>
</property>
<property>
	<name>yarn.resourcemanager.webapp.address</name>
	<value>master:18088</value>
</property>

<!-- 日志的聚合功能，方便我们查看任务执行完成之后的日志记录 -->
<property>
	<name>yarn.log-aggregation-enable</name>
	<value>true</value>
</property>

<!-- 聚合日志的保存时长 -->
<property>
	<name>yarn.log-aggregation.retain-seconds</name>
	<value>604800</value>
</property>
```

7、配置MapReduce计算框架文件mapred-site.xml

```xml
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>

<!-- 开启mapreduce的小任务模式 -->
<property>
	<name>mapreduce.job.ubertask.enable</name>
	<value>true</value>
</property>

<!-- 配置mapreduce 的jobhistory   可以查看我们所有运行完成的任务的一些情况 -->
<property>
	<name>mapreduce.jobhistory.address</name>
	<value>master:10020</value>
</property>

<property>
	<name>mapreduce.jobhistory.webapp.address</name>
	<value>master:19888</value>
</property>
```

**8、配置master的slaves(workers)文件(`3.0之后的版本改名为workers！！！`)**

写入集群的所有主机名，从节点主机名为slave1，slave2，则

ip	主机名

```shell
198.168.126.2	master
198.168.126.3	slave1
198.168.126.4	slave2
/usr/local/hadoop/etc/hadoop/workers
```

9、**不要`hadoop namenode -format`格式化，先克隆两台虚拟机**

推荐拍个快照备份一下

10、格式化文件系统`hadoop namenode -format`

11、start-all.sh



**原因：hadoop3.1.2版本，除了需要在/etc/hadoop/中配置七个文件，还需要在/sbin/目录下对4个脚本文件进行修改。**
**解决：**

七个配置文件
core-site.xml 配置fs.default.name和hadoop.tmp.dir的路径
hdfs-site.xml 配置dfs.replication的dfs默认冗余度为3
mapred-site.xml 配置mapred.job.tracker的计算路径为master:9001

```xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>
<property>
    <name>mapred.job.tracker</name>
    <value>master:9001</name>
</property>
```

yarn-site.xml 配置
hadoop-env.sh 添加变量JAVA_HOME的路径
yarn-env.sh 同上

workers 代替旧版本的slaves文件，加入slaves的IP或配置过/etc/hosts的主机名

四个脚本文件

start-dfs.sh, stop-dfs.sh添加：

```
HDFS_DATANODE_USER=root
HDFS_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```
start-yarn.sh, stop-yarn.sh添加：
``
```
YARN_RESOURCEMANAGER_USER=root
HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```

``

## 1、Hadoop修改配置文件四步走

1、修改配置文件

2、stop-dfs.sh

3、hdsf namenode -format

4、start-dfs.sh

## 2、hdfs-site.xml的可选项

```xml
//以下均为非必需

<!-- 主要用于存放我们的元数据信息的地址，注意这种写法，实际工作当中一定要先确认你的linux的磁盘挂载在哪个路径下面
	/datadisk
	/mnt/resource
	file:///datadisk,file:///mnt/resource
	存储我们的元数据信息，一定要事先确定我们的磁盘在哪里  df  -lh
	fsimage的存放位置-->
<property>
	<name>dfs.namenode.name.dir</name>
	<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas,file:///export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas2</value>
</property>
<!--  定义dataNode数据存储的节点位置，实际工作中，一般先确定磁盘的挂载目录，然后多个目录用，进行分割 
一定不要把数据干到系统盘里面了-->
<property>
	<name>dfs.datanode.data.dir</name>
	<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas,file:///export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas2</value>
</property>
<!--定义我们的元数据的操作日志的存放位置edits的存放位置-->
dfs.namenode.edits.dir
file:///export/servers/hadoop-2.7.5/hadoopDatas/nn/edits
<!-- 元数据检查点保存的位置 -->
<property>
	<name>dfs.namenode.checkpoint.dir</name>
	<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/snn/name</value>
</property>
<!--edits文件的检查点保存位置 -->
<property>
	<name>dfs.namenode.checkpoint.edits.dir</name>
	<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/dfs/snn/edits</value>
</property>
<!-- hdfs的权限控制 -->
<property>
	<name>dfs.permissions</name>
	<value>false</value>
</property>
<!--  文件存储的block块大小 ：128M  -->
<property>
	<name>dfs.blocksize</name>
	<value>134217728</value>
</property>
```

## 3、50700端口关闭

问题1：配置的时候遇到**JAVA_HOME not set**

解决办法：必须在hadoop-env.sh文件中设置JAVA的绝对路径

问题2：能正常启动集群，能看8088端口，但是无法查看50070，但是Namenode和datanode已经能JPS查看

解决办法：首先排除了**防火墙**的问题，后来看需要**格式化**，可能我重新修改hadoop-env之后我没有namenode-format了


问题3：namenode format后，能启动了，但是dfsadmin -report发现datanode没有启动

解决办法，还好，以前有过这个经验，**修改tmp下面的各个slave的VERSION中的clusterID即可，和namenode匹配**。详细见**datanode不启动**http://blog.csdn.net/shizhixin/article/details/50402648 ****

## 4、datanode不启动

**原因：dfs/data/current/VERSION中的clusterID与name中的不一致**

可能是因为每次没有关闭就格式化导致DataNode保存dfs/data/current/VERSION记录的clusterID是上一次格式化的，而不是本次格式化的。（使用./hdfs namenode -format格式化namenode时，会在namenode的dfs.name.dir的路径中保存一个current/VERSION文件，记录clusterID）
**解决方案**
**第一种：**
每次格式化前，要先关闭
./stop-all.sh
然后再格式化
./hdfs namenode -format
最后启动
./start-all.sh

**第二种**
进入/home/hadoop/tmp目录（此目录为namenode的dfs.name.dir配置的路径）删除dfs
rm -rf dfs/
然后再格式化
./hdfs namenode -format
最后启动
./start-all.sh

# Hbase

## 运行报错

Hbase报错:
ERROR: org.apache.hadoop.hbase.ipc.ServerNotRunningYetException: Server is not running yet （服务器尚未运行）

报错原因：这种情况是由于不正常的关闭hadoop，从而导致hadoop进入了安全模式产生的原因。
解决方案：
让hadoop退出安全模式
进入hadoop下的bin
`hdfs dfsadmin -safemode leave`
