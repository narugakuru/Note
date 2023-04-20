# **SpringSecurity安全框架**

### 1、什么是SpringSecurity？

SpringSecurity是一个能够为基于Spring的企业应用系统提供声明式的安全访问控制解决方案的安全框架。它提供了一组可以在Spring应用上下文中配置的Bean，充分利用了IOC、DI和AOP功能，为应用系统提供声明式的安全访问控制功能，减少了企业系统安全控制编写大量重复代码的工作。SpringSecurity的两个核心功能是**认证**和**授权****。**简单来说：认证就是系统认为用户是否能登录，授权就是系统判断用户是否具有权限去做某些事情。



**常见的安全框架：**

- SpringSecurity
- Apache Shiro：一个功能强大且易于使用的Java安全框架，提供了认证，授权，加密和会话管理。



### 2、SpringSecurity快速入门



#### 2.1、创建SpringBoot项目，导入依赖

```html
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
 </dependency>

<!--Spring Security整合Thymeleaf-->
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
   <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
```



#### 2.2、编写静态页面，目录结构如下：

![img](https://cdn.nlark.com/yuque/0/2021/png/21855062/1631780898490-60392010-0e8d-4637-8be0-86d0cad3cef1.png)



#### 2.3、编写首页（Thymeleaf整合了Security）

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec=http://www.thymeleaf.org/extras/spring-security>
<head>
    <meta charset="UTF-8">
    <title>影视播放</title>
</head>
<body>

<h1>首页</h1>

<!--只有用户权限为common的时候才能显示-->
<div sec:authorize="hasRole('common')">
    <h3>普通电影</h3>
    <ul>
        <li><a th:href="@{detail/common/1}">飞驰人生</a></li>
        <li><a th:href="@{detail/common/2}">夏洛特烦恼</a></li>
    </ul>
</div>

<!--只有用户权限为vip的时候，才能显示-->
<div sec:authorize="hasRole('vip')">
    <h3>VIP专享</h3>
    <ul>
        <li><a th:href="@{/detail/vip/vip1}">速度与激情</a></li>
        <li><a th:href="@{/detail/vip/vip2}">流浪地球</a></li>
    </ul>
</div>

<!--如果未登录，才显示登录按钮-->
<div sec:authorize="! isAuthenticated()">
    <a th:href="@{/toLogin}">登录</a>
</div>

<!--如果已经登录了，才显示注销按钮，并且显示用户名和角色-->
<div sec:authorize="isAuthenticated()">
    <a th:href="@{/logout}">注销</a>
    用户名：<span sec:authentication="name"></span>
    角色：<span sec:authentication="principal.authorities"></span>
</div>

</body>
</html>
```



#### 2.4、编写登录页

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<h1>自定义登录页面</h1><hr/>
<form th:action="@{/login}" method="post">
  用户名：<input type="text" name="username" /><br/>
  密码：<input type="password" name="password" /><br/>
  <input type="checkbox" name="remember">记住我
  <input type="submit" value="登录" />
</form>
</body>
</html>
```



#### 2.5、写控制层，跳转到各个页面的路由

```java
@Controller
public class RouteController {

    //跳转到首页
    @RequestMapping({"/","/index"})
    public String toIndex(){
        return "index";
    }

    //跳转到登录页面
    @RequestMapping("/toLogin")
    public String toLogin(){
        return "login";
    }

    //跳转到detail下的各个页面,这里有技巧
    @RequestMapping("/detail/{type}/{path}")
    public String toDetail(@PathVariable("type") String type,@PathVariable("path") String path){
        return "detail/"+type+"/"+path;
    }
}
```

#### 

#### 2.6、编写Security的配置类

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

}
```



#### 2.7、在Security配置类里面进行授权

- 请求授权的规则
- 开启登录页面

- 开启注销页面
- 关闭csrf防护

- 开启记住我功能

```java
	//授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //请求授权的规则
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/detail/common/**").hasRole("common")
                .antMatchers("/detail/vip/**").hasRole("vip");

        //开启登录页面
        http.formLogin()
                .loginPage("/toLogin")//指定登录页面是哪个
                .loginProcessingUrl("/login")//form表单提交的地址,一定要是post请求
                .successForwardUrl("/");//登录成功后跳转的页面

        //开启注销页面
        http.logout()
                .logoutSuccessUrl("/");//注销成功后去首页

        //关闭csrf防护
        http.csrf().disable();

        //开启记住我功能,默认保存两周
        http.rememberMe()
                .rememberMeParameter("remember");//自定义前端参数
    }
```



#### 2.8、在Security配置类里面进行认证

```java
	 //认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //密码需要设置编码器
        BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
         //1，使用内存用户信息，作为测试使用
        auth.inMemoryAuthentication().passwordEncoder(encoder)
                .withUser("root").password(encoder.encode("123456")).roles("common","vip")
                .and()
                .withUser("common").password(encoder.encode("123456")).roles("common")
                .and()
                .withUser("vip").password(encoder.encode("123456")).roles("vip");
    }
```



#### 2.9、完整代码如下：

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //请求授权的规则
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/detail/common/**").hasRole("common")
                .antMatchers("/detail/vip/**").hasRole("vip");

        //开启登录页面
        http.formLogin()
                .loginPage("/toLogin")//指定登录页面是哪个
                .loginProcessingUrl("/login")//form表单提交的地址,一定要是post请求
                .successForwardUrl("/");//登录成功后跳转的页面

        //开启注销页面
        http.logout()
                .logoutSuccessUrl("/");//注销成功后去首页

        //关闭csrf防护
        http.csrf().disable();

        //开启记住我功能,默认保存两周
        http.rememberMe()
                .rememberMeParameter("remember");//自定义前端参数
    }

    //认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //密码需要设置编码器
        BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
         //1，使用内存用户信息，作为测试使用
        auth.inMemoryAuthentication().passwordEncoder(encoder)
                .withUser("root").password(encoder.encode("123456")).roles("common","vip")
                .and()
                .withUser("common").password(encoder.encode("123456")).roles("common")
                .and()
                .withUser("vip").password(encoder.encode("123456")).roles("vip");
    }

}
```