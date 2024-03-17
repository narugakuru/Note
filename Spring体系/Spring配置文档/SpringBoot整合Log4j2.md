
#### 基础配置
```json
server:  
  port: 80  
  
spring:  
  datasource:    url: jdbc:mysql://localhost:3306/demodb?characterEncoding=utf-8&serverTimezone=UTC  
    username: root  
    password: root  
    driver-class-name: com.mysql.cj.jdbc.Driver  
  
mybatis-plus:  
  global-config:    db-config:      id-type: auto    # id生成策略 auto为数据库自增
```


#### log4j2的依赖
	在所有starter里去除logback。
引入slf4j和log4j

```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-test</artifactId>  
    <scope>test</scope>  
    <exclusions>  
        <!-- 排除自带的logback依赖 -->  
        <exclusion>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-logging</artifactId>  
        </exclusion>  
    </exclusions>  
</dependency>  
<!-- 使用slf4j 作为日志门面 -->  
<dependency>  
    <groupId>org.slf4j</groupId>  
    <artifactId>slf4j-api</artifactId>  
    <version>1.7.26</version>  
</dependency>  
<!-- 使用 log4j2 的适配器进行绑定 -->  
<dependency>  
    <groupId>org.apache.logging.log4j</groupId>  
    <artifactId>log4j-slf4j-impl</artifactId>  
    <version>2.9.1</version>  
</dependency>  
<!-- log4j2 日志门面 -->  
<dependency>  
    <groupId>org.apache.logging.log4j</groupId>  
    <artifactId>log4j-api</artifactId>  
    <version>2.11.1</version>  
</dependency>  
<!-- log4j2 日志实面 -->  
<dependency>  
    <groupId>org.apache.logging.log4j</groupId>  
    <artifactId>log4j-core</artifactId>  
    <version>2.11.1</version>  
</dependency>
```

#### 完整依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">  
    <modelVersion>4.0.0</modelVersion>  
    <parent>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-parent</artifactId>  
        <version>2.1.0.RELEASE</version>  
        <relativePath/> <!-- lookup parent from repository -->  
    </parent>  
    <groupId>com.demo</groupId>  
    <artifactId>mybatisdemo</artifactId>  
    <version>0.0.1-SNAPSHOT</version>  
    <name>mybatisdemo</name>  
    <description>Demo project for Spring Boot</description>  
    <properties>  
        <java.version>1.8</java.version>  
        <mysql-connector-java.version>5.1.21</mysql-connector-java.version>  
    </properties>  
  
    <dependencies>  
  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-thymeleaf</artifactId>  
            <exclusions>  
                <!-- 排除自带的logback依赖 -->  
                <exclusion>  
                    <groupId>org.springframework.boot</groupId>  
                    <artifactId>spring-boot-starter-logging</artifactId>  
                </exclusion>  
            </exclusions>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-web</artifactId>  
            <exclusions>  
                <!-- 排除自带的logback依赖 -->  
                <exclusion>  
                    <groupId>org.springframework.boot</groupId>  
                    <artifactId>spring-boot-starter-logging</artifactId>  
                </exclusion>  
            </exclusions>  
        </dependency>  
  
        <!---mybatisPlus及其逆向工程依赖-->  
        <dependency>  
            <groupId>com.baomidou</groupId>  
            <artifactId>mybatis-plus-boot-starter</artifactId>  
            <version>3.4.1</version>  
        </dependency>  
        <dependency>  
            <groupId>com.baomidou</groupId>  
            <artifactId>mybatis-plus-extension</artifactId>  
            <version>3.4.1</version>  
        </dependency>  
        <dependency>  
            <groupId>com.baomidou</groupId>  
            <artifactId>mybatis-plus-generator</artifactId>  
            <version>3.4.1</version>  
        </dependency>  
        <!-- 实体标记-->  
        <dependency>  
            <groupId>org.projectlombok</groupId>  
            <artifactId>lombok</artifactId>  
            <optional>true</optional>  
        </dependency>  
        <!-- 逆向工程代码生成器模板引擎-->  
        <dependency>  
            <groupId>org.apache.velocity</groupId>  
            <artifactId>velocity-engine-core</artifactId>  
            <version>2.1</version>  
        </dependency>  
        <dependency>  
            <groupId>org.freemarker</groupId>  
            <artifactId>freemarker</artifactId>  
            <version>2.3.28</version>  
        </dependency>  
        <!---mysql基础依赖-->  
        <dependency>  
            <groupId>mysql</groupId>  
            <artifactId>mysql-connector-java</artifactId>  
        </dependency>  
        <!-- Control返回数据用 -->  
        <dependency>  
            <groupId>com.alibaba.fastjson2</groupId>  
            <artifactId>fastjson2</artifactId>  
            <version>2.0.15.graal</version>  
        </dependency>  
        <!---SpringBoot基础依赖-->  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-devtools</artifactId>  
            <scope>runtime</scope>  
            <optional>true</optional>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-configuration-processor</artifactId>  
            <optional>true</optional>  
        </dependency>  
        <dependency>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-test</artifactId>  
            <scope>test</scope>  
            <exclusions>  
                <!-- 排除自带的logback依赖 -->  
                <exclusion>  
                    <groupId>org.springframework.boot</groupId>  
                    <artifactId>spring-boot-starter-logging</artifactId>  
                </exclusion>  
            </exclusions>  
        </dependency>  
  
  
        <!-- 使用slf4j 作为日志门面 -->  
        <dependency>  
            <groupId>org.slf4j</groupId>  
            <artifactId>slf4j-api</artifactId>  
            <version>1.7.26</version>  
        </dependency>  
        <!-- 使用 log4j2 的适配器进行绑定 -->  
        <dependency>  
            <groupId>org.apache.logging.log4j</groupId>  
            <artifactId>log4j-slf4j-impl</artifactId>  
            <version>2.9.1</version>  
        </dependency>  
        <!-- log4j2 日志门面 -->  
        <dependency>  
            <groupId>org.apache.logging.log4j</groupId>  
            <artifactId>log4j-api</artifactId>  
            <version>2.11.1</version>  
        </dependency>  
        <!-- log4j2 日志实面 -->  
        <dependency>  
            <groupId>org.apache.logging.log4j</groupId>  
            <artifactId>log4j-core</artifactId>  
            <version>2.11.1</version>  
        </dependency>  
    </dependencies>  
  
    <build>  
        <plugins>  
            <plugin>  
                <groupId>org.springframework.boot</groupId>  
                <artifactId>spring-boot-maven-plugin</artifactId>  
                <configuration>  
                    <excludes>  
                        <exclude>  
                            <groupId>org.projectlombok</groupId>  
                            <artifactId>lombok</artifactId>  
                        </exclude>  
                    </excludes>  
                </configuration>  
            </plugin>  
        </plugins>  
    </build>  
    <repositories>  
        <repository>  
            <id>spring-milestones</id>  
            <name>Spring Milestones</name>  
            <url>https://repo.spring.io/milestone</url>  
            <snapshots>  
                <enabled>false</enabled>  
            </snapshots>  
        </repository>  
        <repository>  
            <id>spring-snapshots</id>  
            <name>Spring Snapshots</name>  
            <url>https://repo.spring.io/snapshot</url>  
            <releases>  
                <enabled>false</enabled>  
            </releases>  
        </repository>  
    </repositories>  
    <pluginRepositories>  
        <pluginRepository>  
            <id>spring-milestones</id>  
            <name>Spring Milestones</name>  
            <url>https://repo.spring.io/milestone</url>  
            <snapshots>  
                <enabled>false</enabled>  
            </snapshots>  
        </pluginRepository>  
        <pluginRepository>  
            <id>spring-snapshots</id>  
            <name>Spring Snapshots</name>  
            <url>https://repo.spring.io/snapshot</url>  
            <releases>  
                <enabled>false</enabled>  
            </releases>  
        </pluginRepository>  
    </pluginRepositories>  
  
</project>
```


#### 配置log4j
application.yaml文件中加入
```json
#日志配置 无特殊需求无需更改  
logging:  
  config: classpath:log4j2.xml  
  level:  
    root: INFO  
    javax.activation: info  
    org.apache.catalina: INFO  
    org.apache.commons.beanutils.converters: INFO  
    org.apache.coyote.http11.Http11Processor: INFO  
    org.apache.http: INFO  
    org.apache.tomcat: INFO  
    org.springframework: INFO  
    com.chinamobile.cmss.bdpaas.resource.monitor: DEBUG
```


#### resource/log4j2.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Configuration>  
    <!--<Configuration status="WARN" monitorInterval="30"> -->  
    <properties>  
        <property name="LOG_HOME">./service-logs</property>  
    </properties>  
    <Appenders>  
        <!--*********************控制台日志***********************-->  
        <Console name="consoleAppender" target="SYSTEM_OUT">  
            <!--设置日志格式及颜色-->  
            <PatternLayout  
                    pattern="%style{%d{ISO8601}}{bright,green} %highlight{%-5level} [%style{%t}{bright,blue}] %style{%C{}}{bright,yellow}: %msg%n%style{%throwable}{red}"  
                    disableAnsi="false" noConsoleNoAnsi="false"/>  
        </Console>  
  
        <!--*********************文件日志***********************-->  
        <!--all级别日志-->  
        <RollingFile name="allFileAppender"  
                     fileName="${LOG_HOME}/all.log"  
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/all-%d{yyyy-MM-dd}-%i.log.gz">  
            <!--设置日志格式-->  
            <PatternLayout>  
                <pattern>%d %p %C{} [%t] %m%n</pattern>  
            </PatternLayout>  
            <Policies>  
                <!-- 设置日志文件切分参数 -->  
                <!--<OnStartupTriggeringPolicy/>-->                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->  
                <TimeBasedTriggeringPolicy/>  
            </Policies>  
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->  
            <DefaultRolloverStrategy max="100"/>  
        </RollingFile>  
  
        <!--debug级别日志-->  
        <RollingFile name="debugFileAppender"  
                     fileName="${LOG_HOME}/debug.log"  
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/debug-%d{yyyy-MM-dd}-%i.log.gz">  
            <Filters>  
                <!--过滤掉info及更高级别日志-->  
                <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>  
            </Filters>  
            <!--设置日志格式-->  
            <PatternLayout>  
                <pattern>%d %p %C{} [%t] %m%n</pattern>  
            </PatternLayout>  
            <Policies>  
                <!-- 设置日志文件切分参数 -->  
                <!--<OnStartupTriggeringPolicy/>-->                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->  
                <TimeBasedTriggeringPolicy/>  
            </Policies>  
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->  
            <DefaultRolloverStrategy max="100"/>  
        </RollingFile>  
  
        <!--info级别日志-->  
        <RollingFile name="infoFileAppender"  
                     fileName="${LOG_HOME}/info.log"  
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log.gz">  
            <Filters>  
                <!--过滤掉warn及更高级别日志-->  
                <ThresholdFilter level="warn" onMatch="DENY" onMismatch="NEUTRAL"/>  
            </Filters>  
            <!--设置日志格式-->  
            <PatternLayout>  
                <pattern>%d %p %C{} [%t] %m%n</pattern>  
            </PatternLayout>  
            <Policies>  
                <!-- 设置日志文件切分参数 -->  
                <!--<OnStartupTriggeringPolicy/>-->                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->  
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>  
                <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->  
                <!--<DefaultRolloverStrategy max="100"/>-->  
            </Policies>  
  
        </RollingFile>  
  
        <!--warn级别日志-->  
        <RollingFile name="warnFileAppender"  
                     fileName="${LOG_HOME}/warn.log"  
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log.gz">  
            <Filters>  
                <!--过滤掉error及更高级别日志-->  
                <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>  
            </Filters>  
            <!--设置日志格式-->  
            <PatternLayout>  
                <pattern>%d %p %C{} [%t] %m%n</pattern>  
            </PatternLayout>  
            <Policies>  
                <!-- 设置日志文件切分参数 -->  
                <!--<OnStartupTriggeringPolicy/>-->                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->  
                <TimeBasedTriggeringPolicy/>  
            </Policies>  
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->  
            <DefaultRolloverStrategy max="100"/>  
        </RollingFile>  
  
        <!--error及更高级别日志-->  
        <RollingFile name="errorFileAppender"  
                     fileName="${LOG_HOME}/error.log"  
                     filePattern="${LOG_HOME}/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log.gz">  
            <!--设置日志格式-->  
            <PatternLayout>  
                <pattern>%d %p %C{} [%t] %m%n</pattern>  
            </PatternLayout>  
            <Policies>  
                <!-- 设置日志文件切分参数 -->  
                <!--<OnStartupTriggeringPolicy/>-->                <!--设置日志基础文件大小，超过该大小就触发日志文件滚动更新-->  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <!--设置日志文件滚动更新的时间，依赖于文件命名filePattern的设置-->  
                <TimeBasedTriggeringPolicy/>  
            </Policies>  
            <!--设置日志的文件个数上限，不设置默认为7个，超过大小后会被覆盖；依赖于filePattern中的%i-->  
            <DefaultRolloverStrategy max="100"/>  
        </RollingFile>  
  
        <!--json格式error级别日志-->  
        <RollingFile name="errorJsonAppender"  
                     fileName="${LOG_HOME}/error-json.log"  
                     filePattern="${LOG_HOME}/error-json-%d{yyyy-MM-dd}-%i.log.gz">  
            <JSONLayout compact="true" eventEol="true" locationInfo="true"/>  
            <Policies>  
                <SizeBasedTriggeringPolicy size="100 MB"/>  
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>  
            </Policies>  
        </RollingFile>  
    </Appenders>  
  
    <Loggers>  
        <!-- 根日志设置 -->  
        <Root level="debug">  
            <AppenderRef ref="allFileAppender" level="all"/>  
            <AppenderRef ref="consoleAppender" level="debug"/>  
            <AppenderRef ref="debugFileAppender" level="debug"/>  
            <AppenderRef ref="infoFileAppender" level="info"/>  
            <AppenderRef ref="warnFileAppender" level="warn"/>  
            <AppenderRef ref="errorFileAppender" level="error"/>  
            <AppenderRef ref="errorJsonAppender" level="error"/>  
        </Root>  
  
        <!--spring日志-->  
        <Logger name="org.springframework" level="debug"/>  
        <!--druid数据源日志-->  
        <Logger name="druid.sql.Statement" level="warn"/>  
        <!-- mybatis日志 -->  
        <Logger name="com.mybatis" level="warn"/>  
        <Logger name="org.hibernate" level="warn"/>  
        <Logger name="com.zaxxer.hikari" level="info"/>  
        <Logger name="org.quartz" level="info"/>  
        <Logger name="com.andya.demo" level="debug"/>  
    </Loggers>  
  
</Configuration>
```

#### 总结
- 因为Springboot自带了logback会和log4j冲突，需要在所有starter里去除logback。