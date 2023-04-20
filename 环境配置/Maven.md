
打包资源文件
```xml
<resources>  
   <resource>  
      <directory>src/main/resources</directory>  
      <excludes>  
         <exclude>**/*.properties</exclude>  
         <exclude>**/*.xml</exclude>  
      </excludes>  
      <filtering>false</filtering>  
   </resource>  
   <resource>  
      <directory>src/main/java</directory>  
      <includes>  
         <include>**/*.properties</include>  
         <include>**/*.xml</include>  
      </includes>  
      <filtering>false</filtering>  
   </resource>  
</resources>
```


## 找不到主类

project structs的artifacts中添加空的jar包，jar包添加模块的输出内容

调用时使用全类名



