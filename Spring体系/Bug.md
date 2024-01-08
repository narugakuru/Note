

## 1. Redis common pool报错
#依赖冲突

![image.png](https://cdn.jsdelivr.net/gh/narugakuru/images/img/20230212161749.png)

原因：common pool版本和springboot默认版本冲突
解决：删去commonpool版本后刷新maven解析，重新compile

