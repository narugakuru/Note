---
tags:
  - Hadoop
---

# 启动集群

启动hadoop hdfs & yarn 服务
【命令】$HADOOP_HOME/sbin/start-all.sh
启动Job History服务
【命令】$HADOOP_HOME/bin/mapred --daemon start historyserver

- hdfs 9870
- yarn 8088
- jobHistory 19888

# 运行wordcounts

【目标】
统计登录日志中各用户登录总次数

【数据集】
email_log.txt文件中，每行文本代表用户一次登录记录

【前置条件】
正常运行hdfs服务，并具有上传文件的权限
正常运行yarn服务

【操作流程】
1）上传数据集【email_log.txt】到hdfs【/datasets/】
\#上传文件
$HADOOP_HOME/bin/hadoop fs -put <local_file_path> <hdfs_dir_path>

2）运行官方WordCount MapReduce作业统计登录日志数据集
$HADOOP_HOME/bin/hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.4.jar wordcount <input_file_hdfs_path> <output_dir_hdfs_path>

3）获取运行统计结果
```
#查看输出目录
$HADOOP_HOME/bin/hadoop fs -ls <output_dir_hdfs_path>
#查看输出结果
$HADOOP_HOME/bin/hadoop fs -cat hdfs://<hdfs_ip>:9000/<output_file_hdfs_path> 
#下载结果到文本
$HADOOP_HOME/bin/hadoop fs -get <hdfs_file_path> <local_path>
```
