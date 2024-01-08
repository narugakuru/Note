
### 运行指令
nohup hive --service metastore -p 9083 >/dev/null &
	这一命令用于启动Hive元数据存储服务，会监听在端口9083上。nohup的作用是在当前终端关闭的时候，它仍然会在后台持续运行，而“>/dev/null”表示将运行的输出结果重定向到/dev/null文件，即不输出任何内容，最后的“&”表示将该命令放入后台运行。
hive --service metastore &
hive --service hiveserver2 &


### 远程访问hive
IDEA中访问hive报错，因为没有开放权限
org.apache.hadoop.ipc.RemoteException: User: root is not allowed to impersonate root
```xml
在core-site.xml添加如下属性，其中
hadoop.proxyuser.root.groups中的root是用户，value里面的root是group
<property>
      <name>hadoop.proxyuser.root.groups</name>
      <value>*</value>
      <description>Allow the superuser oozie to impersonate any members of the group group1 and group2</description>
 </property>
 <property>
      <name>hadoop.proxyuser.root.hosts</name>
      <value>*</value>
      <description>The superuser can connect only from host1 and host2 to impersonate a user</description>
  </property>
```


### 常用函数
#### 一、常用日期函数

1. unix_timestamp:返回当前或指定时间的时间戳	
select unix_timestamp();
select unix_timestamp("2020-10-28",'yyyy-MM-dd');

2. from_unixtime：将时间戳转为日期格式
select from_unixtime(1603843200);

3. current_date：当前日期
select current_date;

4. current_timestamp：当前的日期加时间
select current_timestamp;

5. to_date：抽取日期部分
select to_date('2020-10-28 12:12:12');

6. year：获取年
select year('2020-10-28 12:12:12');

7. month：获取月
select month('2020-10-28 12:12:12');

8. day：获取日
select day('2020-10-28 12:12:12');

9. hour：获取时
select hour('2020-10-28 12:13:14');

10. minute：获取分
select minute('2020-10-28 12:13:14');

11. second：获取秒
select second('2020-10-28 12:13:14');

12. weekofyear：当前时间是一年中的第几周
select weekofyear('2020-10-28 12:12:12');

13. dayofmonth：当前时间是一个月中的第几天
select dayofmonth('2020-10-28 12:12:12');

14. months_between： 两个日期间的月份
select months_between('2020-04-01','2020-10-28');

15. add_months：日期加减月
select add_months('2020-10-28',-3);

16. datediff：两个日期相差的天数
select datediff('2020-11-04','2020-10-28');

17. date_add：日期加天数
select date_add('2020-10-28',4);

18. date_sub：日期减天数
select date_sub('2020-10-28',-4);

19. last_day：日期的当月的最后一天
select last_day('2020-02-30');

20. date_format(): 格式化日期
select date_format('2020-10-28 12:12:12','yyyy/MM/dd HH:mm:ss');




#### 二、常用取整函数
21. round： 四舍五入
select round(3.14);
select round(3.54);

22. ceil：  向上取整
select ceil(3.14);
select ceil(3.54);

floor： 向下取整
23. select floor(3.14);
select floor(3.54);

#### 三、常用字符串操作函数
24. upper： 转大写
select upper('low');

25. lower： 转小写
select lower('low');

26. length： 长度
select length("atguigu");

27. trim：  前后去空格
select trim(" atguigu ");

28. lpad： 向左补齐，到指定长度
select lpad('atguigu',9,'g');

29. rpad：  向右补齐，到指定长度
select rpad('atguigu',9,'g');

30. regexp_replace：使用正则表达式匹配目标字符串，匹配成功后替换！
SELECT regexp_replace('2020/10/25', '/', '-');

#### 四、集合操作
31. size： 集合中元素的个数
select size(friends) from test3;

32. map_keys： 返回map中的key
select map_keys(children) from test3;

33. map_values: 返回map中的value
select map_values(children) from test3;

34. array_contains: 判断array中是否包含某个元素
select array_contains(friends,'bingbing') from test3;

35. sort_array： 将array中的元素排序
select sort_array(friends) from test3;

#### 五、多维分析
36. grouping sets:多维分析



*安装MySQL数据库

### *安装Hive
Hive已上传至Linux主机，并保存到/root目录下
解压解包Hive程序包
【命令】tar -zxvf /root/apache-hive-3.1.3-bin.tar.gz -C /opt

修改Hive程序路径名称
【命令】mv <hive_old_path> /opt/hive

*配置环境变量
【命令】vim /etc/profile.d/hive-env.sh

```sh
export HIVE_HOME=/opt/hive
export PATH=$PATH:$HIVE_HOME/bin
```

使上述环境变量配置生效
【命令】source /etc/profile.d/hive-env.sh

### *运行Hive前准备
前提条件 【Hive 使用 Hadoop 存储】
已成功安装Hadoop，并成功启动hdfs&yarn服务

创建Hive在hdfs上存储目录
【命令】$HADOOP_HOME/bin/hadoop fs -mkdir /tmp
【命令】$HADOOP_HOME/bin/hadoop fs -chmod g+w /tmp


### *部署MySQL JDBC驱动
使用Final Shell上传驱动文件到Linux目录中
把驱动文件移动到HIVE_HOME/lib目录中
【命令】mv <Driver_file_path> $HIVE_HOME/lib/

### *配置Hive服务
编辑$HIVE_HOME/conf/hive-site.xml
【命令】vim $HIVE_HOME/conf/hive-site.xml

```xml
<?xml version="1.0"?>  
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>  
<configuration>  
    <!-- jdbc连接的URL -->  
    <property>  
        <name>javax.jdo.option.ConnectionURL</name>  
        <value><![CDATA[jdbc:mysql://127.0.0.1:3306/hive?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC]]></value>  
    </property>  
  
    <!-- jdbc连接的Driver-->  
    <property>  
        <name>javax.jdo.option.ConnectionDriverName</name>  
        <value>com.mysql.cj.jdbc.Driver</value>  
    </property>  
  
    <!-- jdbc连接的username-->  
    <property>  
        <name>javax.jdo.option.ConnectionUserName</name>  
        <value>root</value>  
    </property>  
  
    <!-- jdbc连接的password -->  
    <property>  
        <name>javax.jdo.option.ConnectionPassword</name>  
        <value>123456</value>  
    </property>  
  
    <!--    日志文件路径 -->  
    <property>  
        <name>hive.server2.logging.operation.log.location</name>  
        <value>/opt/hive/tmp/operation_logs</value>  
    </property>  
  
    <!-- Hive数据存储在HDFS上位置 -->  
    <property>    
      <name>hive.metastore.warehouse.dir</name>    
      <value>/tmp</value>    
    </property>   
      
    <!-- Hive元数据存储的验证 -->  
    <property>  
        <name>hive.metastore.schema.verification</name>  
        <value>false</value>  
    </property>  
     
    <!-- 元数据存储授权  -->  
    <property>  
        <name>hive.metastore.event.db.notification.api.auth</name>  
        <value>false</value>  
    </property>  
  
    <!-- 指定metastore地址 -->  
    <property>  
        <name>hive.metastore.uris</name>  
        <value>thrift://127.0.0.1:9083</value>  
    </property>  
  
  
    <!-- 指定hiveserver2连接的host -->  
    <property>  
        <name>hive.server2.thrift.bind.host</name>  
        <value>master</value>  
    </property>  
  
</configuration>
```

配置hive-env.sh文件
```sh
export HADOOP_HOME=/opt/hadoop  
export HIVE_CONF_DIR=/opt/hive/conf  
export HIVE_AUX_JARS_PATH=/opt/hive/lib
```

*Hive Schema(架构)工具
### 初始化元(meta)数据库，设置为mysql
【命令】$HIVE_HOME/bin/schematool -dbType mysql -initSchema

查询Schema信息
【命令】$HIVE_HOME/bin/schematool -dbType mysql -info

￥￥￥<数据库类型>：derby(默认)，mysql（另安装）


*运行Hive CLI （Command Line Interface）【命令行】方式 ***不推荐
	# 启动并进入命令行交互
	【命令】$HIVE_HOME/bin/hive

	# 命令行执行查询
	【命令】$HIVE_HOME/bin/hive -e 'HQL'

	# 使用静默模式将查询中的数据转储到文件中
	【命令】$HIVE_HOME/bin/hive -S -e 'HQL' > <输出文件路径>

	# 从本地磁盘以非交互方式运行脚本
	【命令】$HIVE_HOME/bin/hive -f <HQ脚本文件路径>

	￥￥￥<HQ脚本文件路径>支持远程URI


### *运行Hive metastore 服务
	# 后台运行（在hive-site.xml中指定配置）
【命令】hive --service metastore &


### *运行Hive HiveServer2 和 Beeline
	# 启动HiveServer2服务
【命令】$HIVE_HOME/bin/hiveserver2
--或者
【命令】$HIVE_HOME/bin/hive --service hiveserver2

 如果使用root登录，需设置HADOOP core-site.xml配置文件，并重启HDFS

```xml
*******以下为输入到core-site.sh文件中内容*********
<property>
	<name>hadoop.proxyuser.hadoop.hosts</name>
	<value>*</value>
</property>

<property>
	<name>hadoop.proxyuser.hadoop.groups</name>
	<value>root</value>
</property>
*******以上为输入到core-site.sh文件中内容*********
```

	# 使用beeline连接HiveServer2服务（以JDBC连接）【默认HiveServer2服务端口：10000】
【命令】$HIVE_HOME/bin/beeline -u jdbc:hive2://<启动HiveServer2服务IP地址>:<HiveServer2服务端口>

	# 命令行执行查询
【命令】$HIVE_HOME/bin/beeline -e 'HQL'

	# 从本地磁盘以非交互方式运行脚本
【命令】$HIVE_HOME/bin/beeline -f <HQ脚本文件路径>

	# 在同一进程中启动 Beeline 和 HiveServer2 以进行测试(交互模式)
【命令】$HIVE_HOME/bin/beeline -u jdbc:hive2://



*浏览HiveServer2的Web用户界面 
	# Web UI提供配置、日志记录、指标和活动会话信息。
	# 默认情况下，Web UI在端口10002。
http://<hs2_ip>:10002/

	# 修改默认配置
	【命令】vim $HIVE_HOME/conf/hive-site.xml

```
<!-- # 配置Web UI端口 -->
<property>
	<name>hive.server2.webui.port</name>
	<value>10002</value>
	<final>false</final>
	<source>file:/opt/hive/conf/hive-site.xml</source>
</property>
```



### 管理Hive配置
	# 默认加载配置文件的目录
【命令】cd $HIVE_HOME/conf/

查看hive默认配置文件内容
【命令】cat $HIVE_HOME/conf/hive-default.xml.template

修改hive配置
【命令】vim $HIVE_HOME/conf/hive-site.xml


*Hive日志（Hive使用log4j 进行日志记录）
	# 日志存储默认位置(/tmp/<user.name>目录中)
	# 查看hive日志内容
	【命令】cat /tmp/<用户名>/hive.log

	# 修改日志存储位置（vim命令 等同于 vi命令）
	【命令】vim $HIVE_HOME/conf/hive-log4j.properties 

	*******以下为hive-log4j.properties文件中内容*********

	hive.log.dir=<日志存储目录路径>

	*******以上为hive-log4j.properties文件中内容*********







