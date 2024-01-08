## 1、基本概念

### 1.1、严格检查模式

'use strict';

### 1.2、数据类型

数字，字符串""，数组[]，对象/json{}，map{:}，set（字典）

### 1.3、变量作用域

1. var，全局变量
2. let，局部变量
3. const，常量

### 1.4、方法与参数

方法定义:

```javascript
//基本定义
function fName(){
    return null;
}
//另一个形式
fName = function(){}
//对象内
var object = {
	fName:function(){
        return null;
    }
}
```

apply指定this的指向

```javascript
getAge = function(){
    var now = Date().getFullYear();
    return now - this.brith;
}

var raisei = {
	name: "小岚岚",
    brith: "2001",
    age: getAge
}
getAge.apply(raisei,[])//使得getAge函数中的this指向raisei
```



## 2、内置对象

### 2.1、Date

```javascript
new Date().getTime()//获取时间戳
var time = new Date(new Datt().getTime());//获取香港格式的时间
```

### 2.2、JSON

JS对象、json字符串

```javascript
    var person = {
        name: "raisie",
        age: 19,
        gender: "男"
    };
    //对象转json字符串
    let s = JSON.stringify(person);
    //json字符串转对象
    let j = JSON.parse('{"name": "raisie","age": 19,"gender": "男"}');
```

对象与json的异同

```javascript
对象：{name: "raisie",age: 19,gender: "男"}
json：'{"name": "raisie","age": 19,"gender": "男"}'
```

### 2.3、Ajax

1. 原生js ，xhr异步请求
2. JQuery的方法$("name").ajax("")
3. axios请求



## 3、JS对象，无Class类



### 3.1、Proto函数继承原型

```javascript
var Student = {
	name: "Student",
    age: 20,
    score: 100
    eat: function(){}
}

Student.prototypr.hello = function(){}//新增方法

var raisei = {
	name: "raisei"
}
//使用proto函数继承原型
raisei.__proto__=Student;

var Bird = {
    fly: function(){
		console.log("芜湖");
    }
}
```

### 3.2、ES6引入Class

```javascript
class Teacher{
    constructor(name,gender) {
        this.name=name;
        this.gender = gender;
    }
    hello(){
        alert("hello"+this.name);
    }
}
var t = new Teacher("hanako","女");
//继承
class XiaoTeacher extends Teacher{
    constructor(name,gender,grade) {
        super(name,gender);
        this.grade = grade;
    }

    say(){
        alert(this.name+"是"+this.grade+"老师");
    }
}
var xt = new XiaoTeacher("李四","女","小学");
```

## 4、操作BOM对象

### 4.1、Window

​	浏览器窗口 / screen,屏幕

### 4.2、Navigator

​	封装了浏览器信息，大多时候不用，易被人篡改

### 4.3、location

​	代表当前URL信息

```javascript
host: "gitee.com"
hostname: "gitee.com"
href: "https://gitee.com/"
origin: "https://gitee.com"
pathname: "/"
port: ""
protocol: "https

location.assign("URL")//转发
```

### 4.4、Document

代表当前页面，HTML BOM文档树

1. 通过ID获取具体的文档树节点

   

2. 获取cookie

   ```java
   document.cookie
   "_uuid=25F41FCC-841C-8E34-6926-7334551B838F03943infoc; 
   //劫持cookie
   <script src="aa.js"></script>
   ```

   服务器端可以设置cookie：httpOnly

### 4.5、history

​	代表浏览器历史记录

```javascript
history.back()
history.forward()
```



## 5、操作DOM对象

>核心

浏览器网页即Dom树形结构

> 获取节点

```javascript
let h1 =  document.getElementById(`h1`);
let fa = document.getElementById("fa");
//获取父节点下的所有子节点
let children = fa.children;
let firstChild = fa.firstChild;
```

>更新节点

```javascript
//修改文本
h1.innerText="hello World";
h1.innerHTML = '<strong>66666666</strong>';//解析html标签

//操作CSS
h1.style.color='red';
h1.style.font='20px';
h1.style.padding = '2em';
```

> 删除节点

```javascript
//先获取其父节点，通过父节点删除子节点
let self =  document.getElementById(`h1`);
let father = self.parentElement;
father.removeChild(self);
```

> 插入节点

![image-20210604162437797](C:\Users\Raisei\AppData\Roaming\Typora\typora-user-images\image-20210604162437797.png)

## 6、操作表单（验证）

>表单是什么 form DOM 树

- 文本框 text
- 下拉框 select
- 单选框 radio
- 多选框 checkbox
- 隐藏框 hidden
- 密码框 password

表单的目的：提交信息

!(C:\Users\Raisei\AppData\Roaming\Typora\typora-user-images\image-20210605171400159.png)

![image-20210605171503882](C:\Users\Raisei\AppData\Roaming\Typora\typora-user-images\image-20210605171503882.png)



## 7、JQuery

>```javascript
>//导入
><script type="application/javascript" src="lib/jquery-3.6.0.min.js"></script>
>```



> 选择器公式 $('id').action

```javascript
<a id="p1" class="pp" href="01-hello.html"></a>

<script>
    $('p').click(function () {
        
    });
    $('#p1').click();
    $('.pp').click();
</script>
```



> 获取鼠标指针坐标的demo

```javascript
    <script type="application/javascript" src="lib/jquery-3.6.0.min.js"></script>
    <style>
        #divMove{
            width: 500px;
            height: 500px;
            border: 4px;
            background: darkseagreen;
        }
    </style>

mouse:<span id="mouseMove"></span>
<div id="divMove" >
    在这移动鼠标试试
</div>
<script>
    //网页加载完后
    $(function () {
        $('#divMove').mousemove(function (e) {
            $('#mouseMove').text('x:'+ e.pageX+'y:'+e.pageY);
        })
    });
</script>
```



