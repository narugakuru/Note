
## 环境配置

#### 保证依赖版本无冲突

```xml
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
```

#### 配置Generator.java文件

```java
// package com.demo.mybatisdemo;  
  
import com.baomidou.mybatisplus.annotation.DbType;  
import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;  
import com.baomidou.mybatisplus.core.toolkit.StringPool;  
import com.baomidou.mybatisplus.core.toolkit.StringUtils;  
import com.baomidou.mybatisplus.generator.AutoGenerator;  
import com.baomidou.mybatisplus.generator.InjectionConfig;  
import com.baomidou.mybatisplus.generator.config.*;  
import com.baomidou.mybatisplus.generator.config.converts.MySqlTypeConvert;  
import com.baomidou.mybatisplus.generator.config.po.TableInfo;  
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;  
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;  
  
import java.util.*;  
  
  
public class Generator {  
      
    private static String moduleName = "mybatisdemo";  
    private static String parent = "com.demo";  
    //表名，用英文,分开  
    private static String tableNames = "user";  
  
    /**  
     * 读取控制台内容  
     */  
    public static String scanner(String tip) {  
        Scanner scanner = new Scanner(System.in);  
        StringBuilder help = new StringBuilder();  
        help.append("请输入" + tip + "：");  
        System.out.println(help.toString());  
        if (scanner.hasNext()) {  
            String ipt = scanner.next();  
            if (!ipt.isEmpty()) {  
                return ipt;  
            }  
        }  
        throw new MybatisPlusException("请输入正确的" + tip + "！");  
    }  
  
    public static void main(String[] args) {  
  
        /**  
         * 代码生成器  
         */  
        AutoGenerator mpg = new AutoGenerator();  
  
        /**  
         * 全局配置  
         */  
        GlobalConfig globalConfig = new GlobalConfig();  
        //生成文件的输出目录  
        String projectPath = System.getProperty("user.dir");  
        globalConfig.setOutputDir(projectPath + "/src/main/java");  
        //Author设置作者  
        globalConfig.setAuthor("Raisei");  
        //是否覆盖文件  
        globalConfig.setFileOverride(true);  
        //生成后打开文件  
        globalConfig.setOpen(false);  
        mpg.setGlobalConfig(globalConfig);  
  
        /**  
         * 数据源配置  
         */  
        DataSourceConfig dataSourceConfig = new DataSourceConfig();  
        // 数据库类型,默认MYSQL  
        dataSourceConfig.setDbType(DbType.MYSQL);  
        //自定义数据类型转换  
        dataSourceConfig.setTypeConvert(new MySqlTypeConvert());  
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/demodb?characterEncoding=utf-8&serverTimezone=GMT%2B8&useSSL=false");  
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");  
        dataSourceConfig.setUsername("root");  
        dataSourceConfig.setPassword("root");  
        mpg.setDataSource(dataSourceConfig);  
  
        /**  
         * 包配置  
         */  
        PackageConfig pc = new PackageConfig();  
//        pc.setModuleName(scanner("模块名:mapper"));  
        pc.setModuleName(moduleName);  
        //父包名。如果为空，将下面子包名必须写全部， 否则就只需写子包名  
        pc.setParent(parent);  
        mpg.setPackageInfo(pc);  
  
  
        /**  
         * 自定义配置  
         */  
        InjectionConfig cfg = new InjectionConfig() {  
            @Override  
            public void initMap() {  
                // to do nothing  
            }  
        };  
  
        /**  
         * 模板  
         */  
        //如果模板引擎是 freemarker        String templatePath = "/templates/mapper.xml.ftl";  
        // 如果模板引擎是 velocity , 需要另外写配置  
        // String templatePath = "/templates/mapper.xml.vm";  
  
  
        /**  
         * 自定义输出配置  
         */  
        List<FileOutConfig> focList = new ArrayList<>();  
        // 自定义配置会被优先输出  
        focList.add(new FileOutConfig(templatePath) {  
            @Override  
            public String outputFile(TableInfo tableInfo) {  
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！  
                return projectPath + "/src/main/resources/mapper/"+ pc.getModuleName()  
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;  
            }  
        });  
        cfg.setFileOutConfigList(focList);  
        mpg.setCfg(cfg);  
  
        /**  
         * 配置模板  
         */  
        TemplateConfig templateConfig = new TemplateConfig();  
  
        // 配置自定义输出模板  
        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别  
        // templateConfig.setEntity("templates/entity2.java");  
        // templateConfig.setService();        // templateConfig.setController();  
        templateConfig.setXml(null);  
        mpg.setTemplate(templateConfig);  
  
        /**  
         * 策略配置  
         */  
        StrategyConfig strategy = new StrategyConfig();  
        //设置命名格式  
        strategy.setNaming(NamingStrategy.underline_to_camel);  
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);  
  
//        strategy.setInclude(scanner("表名,多个英文逗号分割").split(","));  
        strategy.setInclude(tableNames.split(","));  
  
        //实体是否为lombok模型（默认 false）  
        strategy.setEntityLombokModel(true);  
        //生成 @RestController 控制器  
        strategy.setRestControllerStyle(true);  
        //设置自定义继承的Entity类全称，带包名  
        //strategy.setSuperEntityClass("com.demo.mybatisdemo.pojo");  
        //设置自定义继承的Controller类全称，带包名  
        //strategy.setSuperControllerClass("com.demo.mybatisdemo.BaseController");  
        //设置自定义基础的Entity类，公共字段  
//        strategy.setSuperEntityColumns("id");  
        //驼峰转连字符  
        strategy.setControllerMappingHyphenStyle(true);  
        //表名前缀  
        strategy.setTablePrefix(pc.getModuleName() + "_");  
        mpg.setStrategy(strategy);  
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());  
        mpg.execute();  
    }  
}
```
#### 启动Generator然后合并src


## 示例

#### 启动类
```java
package com.demo.mybatisdemo;  
  
import org.mybatis.spring.annotation.MapperScan;  
import org.springframework.boot.SpringApplication;  
import org.springframework.boot.autoconfigure.SpringBootApplication;  
  
@MapperScan("com.demo.mybatisdemo.mapper")  
@SpringBootApplication  
public class MybatisdemoApplication {  
  
    public static void main(String[] args)  {  
        SpringApplication.run(MybatisdemoApplication.class, args);  
    }  
  
}
```
#### 测试类
```java
package com.demo.mybatisdemo;  
  
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;  
import com.demo.mybatisdemo.entity.User;  
import com.demo.mybatisdemo.mapper.UserMapper;  
import lombok.extern.slf4j.Slf4j;  
import org.junit.Test;  
import org.junit.runner.RunWith;  
import org.mybatis.spring.annotation.MapperScan;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.test.context.junit4.SpringRunner;  
  
import java.util.*;  
  
  
@Slf4j  
@SpringBootTest  
@MapperScan("com.demo.mybatisdemo.mapper")  
@RunWith(SpringRunner.class)  
public class MybatisdemoApplicationTests {  
  
    @Autowired  
    private UserMapper userMapper;  
  
    @Test  
    public void contextLoads() {  
        System.out.println(userMapper.selectList(null));  
    }  
  
    @Test  
    public void insert(){  
        User user = new User();  
        user.setUsername("三更草堂222");  
        user.setPassword("7777");  
        int insert = userMapper.insert(user);  
        System.out.println(insert);  
    }  
  
    //模糊查询  
    @Test  
    public void wap(){  
        QueryWrapper wrapper = new QueryWrapper();  
        wrapper.like("username","三更");  
        List<User> list = userMapper.selectList(wrapper);  
        System.out.println(list);  
    }  
}
```


# 总结

- MybatisPlus和Generator版本最好一直，不然容易报错