---
tags:
  - Hadoop
---
Hadoop伪分布式部署（Pseudo-Distributed Mode）
*部署前提
	·已安装JDK1.8，并配置JAVA_HOME环境变量
	·已配置ssh免密登录
	·准备hadoop安装包，上传至/root目录中

*安装
	解压hadoop软件包
	【命令】tar -zxvf /root/hadoop-3.3.4.tar.gz -C /opt
	修改hadoop目录名称
	【命令】mv /opt/hadoop-3.3.4 /opt/hadoop
	
	配置环境变量HADOOP_HOME
	【命令】vim /etc/profile.d/hadoop-env.sh
	配置文件内写入

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=--

```
export HADOOP_HOME=/opt/hadoop-3.3.4

#定义Hadoop启动用户

export HDFS_NAMENODE_USER="root"
export HDFS_DATANODE_USER="root"
export HDFS_SECONDARYNAMENODE_USER="root"
export YARN_RESOURCEMANAGER_USER="root"
export YARN_NODEMANAGER_USER="root"

#pdsh默认使用rsh

export PDSH_RCMD_TYPE=ssh

```

	-=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

	#使环境变量生效
	【命令】source /etc/profile.d/hadoop-env.sh

	#安装pdsh软件[需要访问互联网]
	#针对Ubuntu，Debian Linux版本
	a1)【命令】sudo apt-get install pdsh
	a2)【命令】sudo apt-get install pdsh-rcmd-ssh
	-=-或者-=-
	针对Fedora，CentOS Linux版本
	b1)【命令】sudo yum install pdsh -y
	b2)【命令】sudo yum install pdsh-rcmd-ssh -y

	#关闭防火墙
	【命令】systemctl stop firewalld
	【命令】systemctl disable firewalld


=-=-=-=-=-=-=-=-=-=-=-=-HDFS-=-=-=-=-=-=-=-=-=-=-=-=
*配置Hadoop hdfs服务
	配置etc/hadoop/core-site.xml
	【命令】vim $HADOOP_HOME/etc/hadoop/core-site.xml

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=--
```
	<configuration>
	    <property>
	    	<!-- 指定hdfs服务的文件系统实现，绑定地址和端口号 -->
	        <name>fs.defaultFS</name>
	        <value>hdfs://虚拟机ip:9000</value>
	    </property>
	    <property>
		    <!-- 指定hdfs服务中NameNode数据存储位置，默认存储在/tmp目录下 -->
		    <!-- 如果不修改默认位置，Linux重启会丢失NameNode数据，需要重新执行NameNode format -->
			<name>hadoop.tmp.dir</name>
			<value>/opt/hadoop/tmp</value>
		</property>
	</configuration>
```
	-=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

	配置etc/hadoop/hdfs-site.xml
	【命令】vim $HADOOP_HOME/etc/hadoop/hdfs-site.xml

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=--
```
	<configuration>
	    <property>
	    	<!-- 指定hdfs中文件块副本数为1（默认为3） -->
	        <name>dfs.replication</name>
	        <value>1</value>
	    </property>
		<property>
			<!-- 启动Webdhfs服务 -->
		    <name>dfs.webhdfs.enabled</name>
		    <value>true</value>
		</property>
		<property>
		    <!-- HDFS的权限检查功能(默认值为true) -->
		    <name>dfs.permissions.enabled</name>
		    <value>false</value>
		</property>
	</configuration>
```

	-=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

*启动hdfs服务
	1）格式化NameNode文件系统（仅第一次启动之前）
	【命令】$HADOOP_HOME/bin/hdfs namenode -format

	2）启动hdfs服务的NameNode和DataNode服务
	【命令】$HADOOP_HOME/sbin/start-dfs.sh

*停止hdfs服务
	【命令】$HADOOP_HOME/sbin/stop-dfs.sh

*查看启动的服务进程
	【命令】jps

*验证hdfs启动
	浏览器中访问http://<name_node_ip>:9870/：
	http://xxx.xxx.xxx.xxx:9870/

=-=-=-=-=-=--=-=-=-=-=-YARN-=-=-=-=-=-=-=-=-=-=-=-=
*配置Hadoop yarn服务和MapReduce框架
	配置etc/hadoop/yarn-site.xml:
	【命令】vim $HADOOP_HOME/etc/hadoop/yarn-site.xml

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=--
```
	<configuration>
		<property>
			<!-- 指定Yarn应用的类加载路径 -->
            <name>yarn.application.classpath</name>
            <value>/opt/hadoop/etc/hadoop:/opt/hadoop/share/hadoop/common/lib/*:/opt/hadoop/share/hadoop/common/*:/opt/hadoop/share/hadoop/hdfs:/opt/hadoop/share/hadoop/hdfs/lib/*:/opt/hadoop/share/hadoop/hdfs/*:/opt/hadoop/share/hadoop/mapreduce/*:/opt/hadoop/share/hadoop/yarn:/opt/hadoop/share/hadoop/yarn/lib/*:/opt/hadoop/share/hadoop/yarn/*</value>
        </property>
	    <property>
	    	<!-- 指定Yarn服务中NodeManager上运行的服务列表 -->
	        <name>yarn.nodemanager.aux-services</name>
	        <value>mapreduce_shuffle</value>
	    </property>
	    <property>
	    	<!-- 指定Yarn服务中NodeManager上可以覆盖的环境变量列表 -->
	        <name>yarn.nodemanager.env-whitelist</name>
	        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_HOME,PATH,LANG,TZ,HADOOP_MAPRED_HOME</value>
	    </property>
	</configuration>
	
```
	-=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

	配置etc/hadoop/mapred-site.xml:
	【命令】vim $HADOOP_HOME/etc/hadoop/mapred-site.xml

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=--
	<configuration>
	    <property>
	    	<!-- 指定MapReduce运行作业[job]时框架服务为yarn（默认local） -->
	        <name>mapreduce.framework.name</name>
	        <value>yarn</value>
	    </property>
	    <property>
	    	<!-- 指定MapReduce作业[job]的类库加载路径 -->
	        <name>mapreduce.application.classpath</name>
	        <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
	    </property>
	</configuration>
	-=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

*启动yarn服务
	【命令】$HADOOP_HOME/sbin/start-yarn.sh

*停止yarn服务
	【命令】$HADOOP_HOME/sbin/stop-yarn.sh

*启动hadoop hdfs & yarn 服务
	【命令】$HADOOP_HOME/sbin/start-all.sh

*停止hadoop hdfs & yarn 服务
	【命令】$HADOOP_HOME/sbin/stop-all.sh

*查看启动的服务进程
	【命令】jps

*验证yarn启动
	浏览器中访问http://<resource_manager_ip>:8088/
	http://xxx.xxx.xxx.xxx:8088/


=-=-=-=-=-=--=-=-=-=-=-MapReduce Job History-=-=-=-=-=-=-=-=-=-=-=-=
*配置Hadoop MapReduce Job History服务器
	配置etc/hadoop/mapred-site.xml:
	【命令】vim $HADOOP_HOME/etc/hadoop/mapred-site.xml

	-=-=--=-=-=-=-以下内容-=-=-=-=-=-=-- 
        <property>
        	<!--历史服务器RPC地址和端口-->
            <name>mapreduce.jobhistory.address</name>               
            <value>0.0.0.0:10020</value>
        </property>
        <property>
        	<!--历史服务器Web UI地址和端口-->
            <name>mapreduce.jobhistory.webapp.address </name>
            <value>0.0.0.0:19888</value>
        </property>        
    -=-=--=-=-=-=-以上内容-=-=-=-=-=-=--

*启动Job History服务
	【命令】$HADOOP_HOME/bin/mapred --daemon start historyserver

*停止Job History服务
	【命令】$HADOOP_HOME/bin/mapred --daemon stop historyserver

*查看启动的服务进程
	【命令】jps

*验证Job History启动
	浏览器中访问http://<job_history_server_ip>:19888/
	http://xxx.xxx.xxx.xxx:19888/



=-=-=-=--=-=-=-=-=-=-=-Hadoop日志-=-=-=-=-=-=-=-=-=-=-=
*Hadoop日志说明
hadoop存在多种日志文件，其中master上的日志文件记录全面信息，包括worker上的jobtracker与datanode也会将错误信息写到master中。而worker中的日志主要记录完成的task任务信息。
【master服务器日志】
1、保存在master服务器上的日志包含了tasktracker与datanode上的部分日志会保存在master中，方便出现问题时定位至具体服务器。
2、master中主要有2种日志，分别以log与out作后缀，其中每一个服务角色进程都会产生这2个日志，如jobtracker/ namenode/ tasktracker/ datanode均会分别产生这2个日志文件。这2个文件均是每天生成一个。
3、【log】日志文件通过log4j记录的，大部分应用程序的日志消息都写到该日志文件中，故障诊断的首要步骤即为检查该文件。【此日志文件最重要】
【out】日志文件记录标准输出和标准错误日志，由于大多日志均使用log4j输出至log日志文件中，因此此文件很小或者为空。系统仅保留最新的5个日志。

*日志默认存储位置
【命令】cd $HADOOP_HOME/logs

*修改日志存储位置
【命令】vim /etc/profile.d/hadoop-env.sh
export HADOOP_LOG_DIR=<保存日志路径>

*Hadoop系统服务日志文件名称含义
hadoop-用户名-服务角色名-主机名.log 或 hadoop-用户名-服务角色名-主机名.out
【用户名】启动服务角色的名称。通过环境变量HDFS_NAMENODE_USER，HDFS_DATANODE_USER，HDFS_SECONDARYNAMENODE_USER，YARN_RESOURCEMANAGER_USER，YARN_NODEMANAGER_USER设置的值。
【服务角色名】hdfs服务「NAMENODE，DATANODE，SECONDARYNAMENODE」；yarn服务「RESOURCEMANAGER，NODEMANAGER」
【主机名】查看当前主机名hostname；修改当前主机名hostnamectl

*【log4j】框架日志级别
日志级别:
OFF | 关闭：最高级别，不打印日志。
FATAL | 致命：指明非常严重的可能会导致应用终止执行错误事件。
ERROR | 错误：指明错误事件，但应用可能还能继续运行。
WARN | 警告：指明可能潜在的危险状况。
INFO | 信息：指明描述信息，从粗粒度上描述了应用运行过程。
DEBUG | 调试：指明细致的事件信息，对调试应用最有用。
TRACE | 跟踪：指明程序运行轨迹，比DEBUG级别的粒度更细。
ALL | 所有：所有日志级别，包括定制级别。

级别优先级:
TRACE < DEBUG < INFO < WARN < ERROR < FATAL 
