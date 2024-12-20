---
tags:
  - Mybatis
---
# Mybatis-02

## 1. 注解开发

​	我们也可以使用注解的形式来进行开发，用注解来替换掉xml。 使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 所以我们在实际企业开发中一般都是使用XML的形式。

​	

### 1.1 步骤

①在核心配置文件中配置mapper接口所在的包名

~~~~xml
    <mappers>
        <package name="com.sangeng.dao"></package>
    </mappers>
~~~~

②在接口对应方法上使用注解来配置需要执行的sql

~~~~java
public interface UserDao {

    @Select("select * from user")
    List<User> findAll();

    @Insert("insert into user values(null,#{username},#{age},#{address})")
    void insertUser(User user);

    @Update("UPDATE USER SET age = #{age} , username = #{username},address = #{address} WHERE id = #{id}")
    void updateUser(User user);

    @Delete("delete from user where id = #{id}")
    void deleteById(Integer id);
}

~~~~

③和之前的一样获取Mapper调用方法即可

~~~~java
    public static void main(String[] args) throws IOException {
        //定义mybatis配置文件的路径
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //获取Sqlsession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取UserDao实现类对象
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        //调用方法测试
        List<User> userList = userDao.findAll();
        System.out.println(userList);
        //释放资源
        sqlSession.close();
    }
~~~~





## 2.动态SQL

​	在实际开发中的SQL语句没有之前的这么简单，很多时候需要根据传入的参数情况动态的生成SQL语句。Mybatis提供了动态SQL相关的标签让我们使用。



### 2.1  if

​	可以使用if标签进行条件判断，条件成立才会把if标签中的内容拼接进sql语句中。

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
         select * from user
         where  id = #{id}
        <if test="username!=null">
           and username = #{username}
        </if>
    </select>
~~~~

如果参数username为null则执行的sql为：**select * from user where id = ?** 

如果参数username不为null则执行的sql为：**select * from user where id = ? and username = ?** 



**注意：在test属性中表示参数的时候不需要写#{}，写了会出问题。**



### 2.2 trim

​	可以使用该标签动态的添加前缀或后缀，也可以使用该标签动态的消除前缀。



#### 2.2.1 prefixOverrides属性

​	用来设置需要被清除的前缀,多个值可以用|分隔，注意|前后不要有空格。例如： and|or

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefixOverrides="and|or" >
            and
        </trim>
    </select>
~~~~

最终执行的sql为： select * from user



#### 2.2.2 suffixOverrides属性

​	用来设置需要被清除的后缀,多个值可以用|分隔，注意|前后不要有空格。例如： and|or

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim suffixOverrides="like|and" >
            where 1=1 like
        </trim>
    </select>
~~~~

最终执行的sql为： select * from user  去掉了后缀like





#### 2.2.3 prefix属性

​	用来设置动态添加的前缀，如果标签中有内容就会添加上设置的前缀

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefix="where" >
           1=1
        </trim>
    </select>
~~~~

最终执行的sql为：select * from user where 1=1   动态增加了前缀where



#### 2.2.4 suffix属性

​	用来设置动态添加的后缀，如果标签中有内容就会添加上设置的后缀

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim suffix="1=1" >
           where
        </trim>
    </select>
~~~~

最终执行的sql为：select * from user where 1=1   动态增加了后缀1=1



#### 2.2.5 动态添加前缀where 并且消除前缀and或者or

~~~~java
User findByCondition(@Param("id") Integer id,@Param("username") String username);
~~~~

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <trim prefix="where" prefixOverrides="and|or" >
            <if test="id!=null">
                id = #{id}
            </if>
            <if test="username!=null">
                and username = #{username}
            </if>
        </trim>
    </select>
~~~~

调用方法时如果传入的id和username为null则执行的SQL为：select * from user

调用方法时如果传入的id为null，username不为null，则执行的SQL为：select * from user where username = ?



### 2.2 where

​	where标签等价于：

~~~~xml
<trim prefix="where" prefixOverrides="and|or" ></trim>
~~~~

​	可以使用where标签动态的拼接where并且去除前缀的and或者or。

例如：

~~~~xml
    <select id="findByCondition" resultType="com.sangeng.pojo.User">
        select * from user
        <where>
            <if test="id!=null">
                id = #{id}
            </if>
            <if test="username!=null">
                and username = #{username}
            </if>
        </where>
    </select>
~~~~

如果id和username都为null，则执行的sql为：**select * from user **

如果id为null，username不为null，则执行的sql为：**select * from user  where username = ? **



### 2.3 set

​	set标签等价于

~~~~xml
<trim prefix="set" suffixOverrides="," ></trim>
~~~~

​	可以使用set标签动态的拼接set并且去除后缀的逗号。

例如：

~~~~xml
    <update id="updateUser">
        UPDATE USER
        <set>
            <if test="username!=null">
                username = #{username},
            </if>
            <if test="age!=null">
                age = #{age},
            </if>
            <if test="address!=null">
                address = #{address},
            </if>
        </set>
        where id = #{id}
    </update>
~~~~

如果调用方法时传入的User对象的id为2，username不为null，其他属性都为null则最终执行的sql为：UPDATE USER SET username = ? where id = ? 



### 2.4 foreach

​	可以使用foreach标签遍历集合或者数组类型的参数，获取其中的元素拿来动态的拼接SQL语句。

例如：

方法定义如下

~~~~java
List<User> findByIds(@Param("ids") Integer[] ids);
~~~~

如果期望动态的根据实际传入的数组的长度拼接SQL语句。例如传入长度为4个数组最终执行的SQL为：

~~~~sql
select * from User WHERE id in( ? , ? , ? , ?, ? ) 
~~~~

则在xml映射文件中可以使用以下写法

~~~~xml
	<select id="findByIds" resultType="com.sangeng.pojo.User">
        select * from User
        <where>
            <foreach collection="ids" open="id in(" close=")" item="id" separator=",">
                #{id}
            </foreach>
        </where>
    </select>
~~~~

**collection：表示要遍历的参数。**

**open:表示遍历开始时拼接的语句**

**item：表示给当前遍历到的元素的取的名字**

**separator：表示每遍历完一次拼接的分隔符**

**close：表示最后一次遍历完拼接的语句**



**注意：如果方法参数是数组类型，默认的参数名是array，如果方法参数是list集合默认的参数名是list。建议遇到数组或者集合类型的参数统一使用@Param注解进行命名。**



### 2.5 choose、when、otherwise

​	当我们不想使用所有的条件，而只是想从多个条件中选择一个使用时。可以使用choose系列标签。类似于java中的switch。

例如:



接口中方法定义如下

~~~~java
List<User> selectChose(User user);
~~~~



期望：

​		如果user对象的id不为空时就通过id查询。

​		如果id为null,username不为null就通过username查询。

​		如果id和username都会null就查询id为3的用户



xml映射文件如下

~~~~xml
 	<select id="selectChose" resultType="com.sangeng.pojo.User">
        select * from user
        <where>
            <choose>
                <when test="id!=null">
                    id = #{id}
                </when>
                <when test="username!=null">
                    username = #{username}
                </when>
                <otherwise>
                    id = 3
                </otherwise>
            </choose>
        </where>
    </select>
~~~~



- **choose类似于java中的switch**

- **when类似于java中的case**

- **otherwise类似于java中的dufault**

  

​	一个choose标签中最多只会有一个when中的判断成立。从上到下去进行判断。如果成立了就把标签体的内容拼接到sql中，并且不会进行其它when的判断和拼接。如果所有的when都不成立则拼接otherwise中的语句。



## 3. SQL片段抽取

​	我们在xml映射文件中编写SQL语句的时候可能会遇到重复的SQL片段。这种SQL片段我们可以使用sql标签来进行抽取。然后在需要使用的时候使用include标签进行使用。

例如：

~~~~xml
    <sql id="baseSelect" >id,username,age,address</sql>
    <select id="findAll" resultType="com.sangeng.pojo.User">
        select <include refid="baseSelect"/>  from user
    </select>
~~~~

最终执行的sql为： **select id,username,age,address from user** 



