Adblocker

[![语雀](https://gw.alipayobjects.com/zos/rmsportal/XuVpGqBFxXplzvLjJBZB.svg)](https://www.yuque.com/)

[微服务分布式](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6)/[**Mybatis Plus**](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/sns47n)

[查看原始文档](https://www.yuque.com/xiaohouxiangxueqianduan/xb7ezn/sns47n)

# Mybatis Plus

##  1、概述 

MybatisPlus是一款Mybatis增强工具，用于简化开发，提高效率。

它在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

官网： https://mp.baomidou.com/

#### 目录

[Mybatis Plus](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/sns47n)

[SpringSecurity安全框架](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/tfmlod)

[SpringBoot--雷神](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/gxxa3w)

[01-SpringBoot入门](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/ds3zn6)

[02-了解自动配置](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/vdzibb)

[Vue](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/sbzkpg)

[基础入门](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/pv9mcd)

[Git](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/sr0cqv)

[Linux](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/iixd22)

[Redis缓存技术](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/kvghb5)

[SpringBoot--三更](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/drdzal)

[SpringBoot-基础入门](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/fqy69h)

[SpringBoot-常见场景](https://www.yuque.com/books/share/939afc68-c3e8-40f9-8365-873613e865b6/lei23y)



##  2、快速入门 

 #### 2.1、准备工作 

 ①准备数据 

SQL



```sql
CREATE TABLE `user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `user_name` varchar(20) NOT NULL COMMENT '用户名',
  `password` varchar(20) NOT NULL COMMENT '密码',
  `name` varchar(30) DEFAULT NULL COMMENT '姓名',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  `address` varchar(100) DEFAULT NULL COMMENT '地址',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

insert  into `user`(`id`,`user_name`,`password`,`name`,`age`,`address`) values (1,'ruiwen','123','瑞文',12,'山东'),(2,'gailun','1332','盖伦',13,'平顶山'),(3,'timu','123','提姆',22,'蘑菇石'),(4,'daji','1222','妲己',221,'狐山');
```



 ②创建SpringBoot工程 

添加依赖

XML



```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.0</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```



 ③准备实体类 





```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Long id;
    private String userName;
    private String password;
    private String name;
    private Integer age;
    private String address;
}
```





 #### 2.2、使用MybatisPlus 

①添加依赖





```xml
<dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-boot-starter</artifactId>
      <version>3.4.3</version>
</dependency>

<dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-</artifactId>
 </dependency>
```



②配置数据库信息

YAML



```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: gududehou520
    driver-class-name: com.mysql.cj.jdbc.Driver
```



③创建Mapper接口

创建Mapper接口继承BaseMapper接口





```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
}
```

BaseMapper接口中提供了很多常用方法，所以我们只需要直接从容器中获取Mapper就可以操作了，不需要自己去编写SQL语句。



④配置Mapper扫描

如果不想在每一个Mapper接口中添加@Mapper注解的话

可以在启动类上面配置我们的Mapper在哪个包。





```java
@SpringBootApplication
@MapperScan("com.monkey.mapper")
public class MybatisPlus01Application {
    public static void main(String[] args) {
        SpringApplication.run(MybatisPlus01Application.class, args);
    }
}
```



⑤测试Mapper





```java
@SpringBootTest
class MybatisPlus01ApplicationTests {

    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        System.out.println(userMapper.selectList(null));
    }
}
```

  

##  3、常用设置 

 #### 3.1、设置表映射规则 

默认情况下，MP操作的表名就是实体类的类名。但是在表名和类名不一致的情况下，就需要我们自己设置映射规则。



 3.1.1、单独设置 

可以在实体类的类名上加上@TableName注解进行标识，例如：

如果表名是tb_user，而实体类名是User，则可以使用以下写法。





```java
@TableName("tb_user")
public class User {
  //....
}
```



 3.1.2、全局设置表名前缀 

一般一个项目表名的前缀都是统一风格，这个时候如果一个一个设置，就太麻烦了。我们可以通过配置来设置全局的表名前缀。

例如：如果一个项目中所有的表名，相比于类名，都只是多了一个前缀：tb_ ，这里可以使用如下方式配置：

YAML



```yaml
mybatis-plus:
  global-config:
    db-config:
      table-prefix: tb_ #表名前缀
```



 #### 3.2、设置主键生成策略 

 3.2.1、测试代码 





```java
 @Test
    void contextLoads() {
        User user = new User();
        user.setUserName("三更草堂222");
        user.setPassword("7777");
        int r = userMapper.insert(user);
        System.out.println(r);
    }
```

默认情况下，使用MybatisPlus插入数据时，如果我们没有设置主键生成策略的话，默认采用的策略是基于雪花算法的自增id，

而不是数据库的自增id。

![Snipaste_2021-09-29_21-24-19.png](https://cdn.nlark.com/yuque/0/2021/png/21855062/1632997199742-471f52cc-bba6-4e80-a2a8-0e71d76ce8ce.png)



这里的策略就是基于雪花算法的。



 3.2.2、单独设置 

若需要使用别的策略，那在定义实体类的时候，要在代表主键的字段上面加上@TableId 注解，使用type属性指定主键生成策略。

例如：我们要设置主键自动增长则可以设置如下：





```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String userName;
    private String password;
    private String name;
    private Integer age;
    private String address;
}
```

全部主键策略定义在了枚举类IdType中，IdType有如下的取值：

●AUTO
数据库ID自增，依赖于数据库。该类型请确保数据库设置了 ID自增 否则无效

●NONE
​      未设置主键类型。若在代码中没有手动设置主键，则会根据主键的全局策略自动生成（基于雪花算法的自增ID）

●INPUT
需要手动设置主键，若不设置。插入操作生成SQL语句时，主键这一列的值会是null。

●ASSIGN_ID
当没有手动设置主键，即实体类中的主键属性为空时，才会自动填充，使用雪花算法

●ASSIGN_UUID
当实体类的主键属性为空时，才会自动填充，使用UUID


 3.2.3、全局设置 
YAML
```yaml
mybatis-plus:
  global-config:
    db-config:
      id-type: auto    # id生成策略 auto为数据库自增
```


 #### 3.3、设置字段映射关系 

默认情况下，MP会根据实体类的属性名去映射表的列名。

如果数据库的列表和实体类的属性名不一致的话，我们可以使用@TableField注解的value属性去设置映射关系。

例如：如果一个表中的一个列名叫address，而实体类中的属性名为addressStr，则可以使用如下方式进行配置。
```java
@TableField("address")
private String addressStr;
```



 #### 3.4、设置字段和列名的驼峰映射 

默认情况下，MP会开启字段名与列名的驼峰映射。

即从经典数据库列名A_COLUMN（下划线命名）到经典属性名aColumn（驼峰命名）的类似映射。

如果需要关闭驼峰映射的话，我们可以这样配置：

YAML
```yaml
mybatis-plus:
  configuration:
    map-underscore-to-camel-case: false
```



 #### 3.5、日志 

如果需要打印MP操作对应的SQL语句等，我们可以这样配置：

YAML

```yaml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```



##  4、基本使用 

 #### 4.1、插入数据 

使用insert方法来实现数据的插入

```java
  @Test
    void contextLoads() {
        User user = new User();
        user.setUserName("小猴子");
        user.setPassword("7777");
        userMapper.insert(user);
    }
```

 #### 4.2、删除操作 

使用deleteXXX方法来实现数据的删除

根据id删除单个数据

```java
  @Test
    void contextLoads() {
        userMapper.deleteById(2);
    }
```


根据id删除多个数据

```java
  @Test
    void contextLoads() {
        List<Integer> ids = new ArrayList<>();
        ids.add(2);
        ids.add(3);
        userMapper.deleteBatchIds(ids);//参数是一个集合
    }
```



把符合条件的删了
```java
  @Test
    void contextLoads() {
        Map<String,Object> map=new HashMap<>();
        map.put("name","提姆");
        map.put("age",22);
        userMapper.deleteByMap(map);//把name为提姆并且age为22的全删了
    }
```



 #### 4.3、更新操作 

可以使用updateXXX方法来实现数据的删除

```java
  @Test
    void contextLoads() {
        //把id为2的用户年龄改为99，注意id为long类型
        User user = new User();
        user.setId(2l);
        user.setAge(99);
        userMapper.updateById(user);
    }
```




##  5、条件构造器Wrapper 

####  5.1、概述 

在实际操作数据库的时候会涉及到很多条件。所以MP为我们提供了一个功能强大的条件构造器Wrapper。

使用它，可以让我们非常方便的构造条件。



其继承体系如下：

![Snipaste_2021-09-29_21-24-19.png](https://cdn.nlark.com/yuque/0/2021/png/21855062/1633019526382-8505ffac-4aaa-4e0a-93b7-df7e0bf214eb.png)





●在其子类AbstractWrapper中提供了很多用于构造Where条件的方法。

●AbstractWrapper的子类QueryWrapper则额外提供了用于针对Select语法的select方法。可以用来设置查询哪些列。

●AbstractWrapper的子类UpdateWrapper则额外提供了用于针对SET语法的set方法。可以用来设置对哪些列进行更新。

●完整的AbstractWrapper方法可以参照：https://baomidou.com/guide/wrapper.html#abstractwrapper

总而言之：只有where的话，用AbstractWrapper，有select用QueryWrapper，有set用UpdateWrapper



####  5.2、常用AbstractWrapper方法 

*●*eq：equals，等于

*●*gt：greater than ，大于 >

*●*ge：greater than or equals，大于等于≥

*●*lt：less than，小于<

*●*le：less than or equals，小于等于≤

*●*between：相当于SQL中的BETWEEN

*●*like：模糊匹配。like("name","黄")，相当于SQL的name like '%黄%'

*●*likeRight：模糊匹配右半边。likeRight("name","黄")，相当于SQL的name like '黄%'

*●*likeLeft：模糊匹配左半边。likeLeft("name","黄")，相当于SQL的name like '%黄'

*●*notLike：notLike("name","黄")，相当于SQL的name not like '%黄%'

**●**isNull

**●**isNotNull

*●*and：SQL连接符AND

*●*or：SQL连接符OR

*●*in： in(“age",{1,2,3})相当于 age in(1,2,3)

*●*groupBy： groupBy("id","name")相当于 group by id,name

*●*orderByAsc：orderByAsc("id","name")相当于 order by id ASC,name ASC

*●*orderByDesc：orderByDesc ("id","name")相当于 order by id DESC,name DESC



 示例一 

SQL语句如下：

```sql
SELECT 
  id,user_name,PASSWORD,NAME,age,address 
FROM 
  USER 
WHERE 
  age > 18 AND address = '狐山'
```

如果使用Wrapper，写法如下：

```java
  @Test
    void contextLoads() {
        QueryWrapper wrapper=new QueryWrapper();
        wrapper.gt("age",18);//  age > 18
        wrapper.eq("address","狐山");//  address='狐山'
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```





 示例二 

SQL语句如下：

```sql
SELECT 
  id,user_name,PASSWORD,NAME,age,address 
FROM 
  USER 
WHERE 
  id IN(1,2,3) AND 
  age BETWEEN 12 AND 29 AND 
  address LIKE '%山%'
```



如果使用Wrapper，其写法如下

```java
  @Test
    void contextLoads() {
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper.in("id",1,2,3);//id IN(1,2,3)
        wrapper.between("age",12,29);//age BETWEEN 12 AND 29
        wrapper.like("address","山");//address LIKE '%山%'
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```





 示例三 

SQL语句如下

```sql
SELECT 
  id,user_name,PASSWORD,NAME,age,address 
FROM 
  USER 
WHERE 
  id IN(1,2,3) AND 
  age > 10 
ORDER BY 
  age DESC
```



如果使用Wrapper，其写法如下

```java
  @Test
    void contextLoads() {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.in("id",1,2,3);//id IN(1,2,3) AND 
        queryWrapper.gt("age",10);//age > 10 
        queryWrapper.orderByDesc("age");//ORDER BY age DESC
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```







####  5.3、常用QueryWrapper方法 

QueryWrapper的select可以设置需要查询的列，select方法有三种重载形式，可以根据场景选择



 示例一 

第一种重载形式：select(String... sqlSelect) 方法，使用该发放进行测试



SQL语句如下：

```
SELECT 
  id,user_name
FROM 
  USER
```





MP写法如下：

```java
  @Test
    void contextLoads() {
       QueryWrapper<User> queryWrapper=new QueryWrapper<>();
       queryWrapper.select("id","user_name");
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```

你需要查哪些字段，只需要在select方法中加上去就行了



 示例二 

第二种重载形式：select(Class<T> entityClass, Predicate<TableFieldInfo> predicate)方法，使用该方法进行测试



SQL语句如下：

```sql
SELECT 
  id,user_name
FROM 
  USER 
```



MP写法如下：

```java
  @Test
    void contextLoads() {
        QueryWrapper<User> queryWrapper=new QueryWrapper<>();
        //进行构造条件
        queryWrapper.select(User.class, new Predicate<TableFieldInfo>() {
            @Override
            public boolean test(TableFieldInfo tableFieldInfo) {
                return "user_name".equals(tableFieldInfo.getColumn());
            }
        });
        List<User> users = userMapper.selectList(queryWrapper);//把条件放进去
        System.out.println(users);
    }
```



如果还想再增加查询一个password字段，可以这样

```java
@Test
    void contextLoads() {
        QueryWrapper<User> queryWrapper=new QueryWrapper<>();
        queryWrapper.select(User.class, new Predicate<TableFieldInfo>() {
            @Override
            public boolean test(TableFieldInfo tableFieldInfo) {
                return "user_name".equals(tableFieldInfo.getColumn())||"password".equals(tableFieldInfo.getColumn());
            }
        });
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```



在我看来，你想要查询哪些字段，你就返回哪个字段，因为这个重载的test方法不止调用过一次，可以测试一下。

```java
@Test
    void contextLoads() {
        QueryWrapper<User> queryWrapper=new QueryWrapper<>();
        queryWrapper.select(User.class, new Predicate<TableFieldInfo>() {
            int i=1;
            @Override
            public boolean test(TableFieldInfo tableFieldInfo) {
                System.out.println("这是我调用的第"+i+"次");
                i++;
                return "user_name".equals(tableFieldInfo.getColumn())||"password".equals(tableFieldInfo.getColumn());
            }
        });
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```



我们看控制台打印输出

![Snipaste_2021-09-29_21-24-19.png](https://cdn.nlark.com/yuque/0/2021/png/21855062/1633051360404-aef28638-7112-4139-a47b-570ebbb3d006.png)





我们再看一下数据库字段。

![Snipaste_2021-09-29_21-24-19.png](https://cdn.nlark.com/yuque/0/2021/png/21855062/1633051412623-51e059fd-cfb2-451b-a1f5-270f1e8ebb73.png)





显而易见，数据库字段加上主键id，总共有6个字段，但是该方法却只调用了5次，因此又可以得出一个结论：

该方法会自动过滤掉数据库的主键，因此就算你不查主键，也会自动帮你查出来。





 示例三 

第三种重载形式：select(Predicate<TableFieldInfo> predicate)方法，使用该方法进行测试

方法第一个参数为Predicate类型，可以使用lambda的写法，过滤要查询的字段 (主键除外) 。



SQL语句如下

```sql
SELECT 
  id,user_name,PASSWORD,NAME,age 
FROM 
  USER
```




可以看出，这需要查询的字段有点多，于是可以使用该重载形式，过滤掉不需要查询的字段（address）



MP写法如下：

```java
  @Test
    void contextLoads() {
       QueryWrapper<User> queryWrapper=new QueryWrapper<>(new User());
       queryWrapper.select(new Predicate<TableFieldInfo>() {
           @Override
           public boolean test(TableFieldInfo tableFieldInfo) {
               return !"address".equals(tableFieldInfo.getColumn());
           }
       });
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```



注意：别忘了，使用这种重载形式的时候，QueryWrapper里面要传一个实体类进去。

其实：使用第二种重载形式也能达到这种过滤的效果。

```java
  @Test
    void contextLoads() {
       QueryWrapper<User> queryWrapper=new QueryWrapper<>();
       queryWrapper.select(User.class, new Predicate<TableFieldInfo>() {
           @Override
           public boolean test(TableFieldInfo tableFieldInfo) {
               return !"address".equals(tableFieldInfo.getColumn());
           }
       });
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```





####  5.4、常用UpdateWrapper方法 

我们在前面使用update方法的时候，需要创建一个实体类对象传入，用来指定需要更新的列以及对应的值。但是，如果需要更新的列较少时，再去创建一个实体类对象的话，就会显得有点麻烦和复杂。

于是，我们可以使用UpdateWrapper的set方法来设置需要更新的列以及对应的值，同时这种方式也可以使用Wrapper去指定更加复杂的条件。


 示例 

SQL语句如下：
```
UPDATE 
  USER
SET 
  age = 99
where 
  id > 1
```

我们想把id大于1的用户的年龄修改为99，则可以使用如下写法：

```java
  @Test
    void contextLoads() {
        UpdateWrapper<User> updateWrapper=new UpdateWrapper<>();
        updateWrapper.gt("id",1);//id > 1
        updateWrapper.set("age",99);//set age=99
        userMapper.update(null,updateWrapper);
    }
```


####  5.5、条件构造器 

我们前面在使用条件构造器时列名都是用字符串的形式去指定。这种方式无法在编译期确定列名的合法性。

所以MP提供了一个Lambda条件构造器可以让我们直接以实体类的方法引用的形式来指定列名。这样就可以弥补上述缺陷。

 示例 

SQL语句如下：

```sql
SELECT 
  id,user_name,PASSWORD,NAME,age,address 
FROM 
  USER 
WHERE 
  age > 18 AND address = '狐山'
```



如果使用之前的条件构造器写法如下

```java
    @Test
     void contextLoads(){
        QueryWrapper<User> queryWrapper = new QueryWrapper();
        queryWrapper.gt("age",18);
        queryWrapper.eq("address","狐山");
        List<User> users = userMapper.selectList(queryWrapper);
    }
```


如果使用Lambda条件构造器写法如下：

```java
    @Test
     void contextLoads() {
        LambdaQueryWrapper<User> queryWrapper=new LambdaQueryWrapper<>();
        queryWrapper.gt(User::getAge,18);
        queryWrapper.eq(User::getAddress,"狐山");
        List<User> users = userMapper.selectList(queryWrapper);
        System.out.println(users);
    }
```



##  6、自定义SQL 

虽然MP为我们提供了很多常用的方法，并且也提供了条件构造器。但是如果真的遇到了复制的SQL时，我们还是需要自己去定义方法，自己去写对应的SQL，这样SQL也更有利于后期维护。



因为MP是对mybatis做了增强，所以还是支持之前Mybatis的方式去自定义方法。

同时也支持在使用Mybatis的自定义方法时使用MP的条件构造器帮助我们进行条件构造，接下去我们分别来讲讲。



####  6.1、准备工作 

 ①准备数据 

SQL


```sql
CREATE TABLE `orders` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `price` int(11) DEFAULT NULL COMMENT '价格',
  `remark` varchar(100) DEFAULT NULL COMMENT '备注',
  `user_id` int(11) DEFAULT NULL COMMENT '用户id',
  `update_time` timestamp NULL DEFAULT NULL COMMENT '更新时间',
  `create_time` timestamp NULL DEFAULT NULL COMMENT '创建时间',
  `version` int(11) DEFAULT '1' COMMENT '版本',
  `del_flag` int(1) DEFAULT '0' COMMENT '逻辑删除标识,0-未删除,1-已删除',
  `create_by` varchar(100) DEFAULT NULL COMMENT '创建人',
  `update_by` varchar(100) DEFAULT NULL COMMENT '更新人',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

/*Data for the table `orders` */

insert  into `orders`(`id`,`price`,`remark`,`user_id`,`update_time`,`create_time`,`version`,`del_flag`,`create_by`,`update_by`) values (1,2000,'无',2,'2021-08-24 21:02:43','2021-08-24 21:02:46',1,0,NULL,NULL),(2,3000,'无',3,'2021-08-24 21:03:32','2021-08-24 21:03:35',1,0,NULL,NULL),(3,4000,'无',2,'2021-08-24 21:03:39','2021-08-24 21:03:41',1,0,NULL,NULL);
```

  

 ②创建实体类 


```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Orders  {

    private Long id;
    /**
     * 价格
     */
    private Integer price;
    /**
     * 备注
     */
    private String remark;
    /**
     * 用户id
     */
    private Integer userId;
    /**
     * 更新时间
     */
    private LocalDateTime updateTime;
    /**
     * 创建时间
     */
    private LocalDateTime createTime;
    /**
     * 版本
     */
    private Integer version;
    /**
     * 逻辑删除标识,0-未删除,1-已删除
     */
    private Integer delFlag;
}
```



####  6.2、Mybatis方式 

 ①定义方法 

在Mapper接口中定义方法

```java
@Mapper
public interface OrdersMapper {
    Orders findOrderById(long id);
}
```


 ②创建xml 

先配置xml文件的存放目录

YAML
```yaml
  mapper-locations: classpath*:/mapper/**/*.xml
```



创建对应的xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.monkey.mapper.OrdersMapper">
</mapper>
```


 ③编写SQL 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.monkey.mapper.OrdersMapper">

    <select id="findOrderById" resultType="com.monkey.domain.Orders">
        select * from orders where id=#{id}
    </select>
</mapper>
```


####  6.3、Mybatis方式结合条件构造器 

我们在使用上述方式自定义方法时，如果也希望我们的自定义方法能像MP自带方法一样使用条件构造器的话，只需要使用如下方式即可。


 ①方法定义中添加Wrapper类型的参数 

添加Wrapper类型的参数，并且注意给其指定参数名。

 ②在SQL语句中获取Wrapper拼接的SQL片段进行拼接 





