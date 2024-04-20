---
tags:
  - Hadoop
---
[(125条消息) HIVE综合练习-SQL练习之影评案例_诺卡先生的博客-CSDN博客](https://blog.csdn.net/weixin_44202489/article/details/106469170)

### 分别求男性，女性当中评分最高的10部电影

（性别，电影名，影评分）
（1）分析思路：

1、需求字段：性别t_user.sex \#电影名　t_movie.moviename \# 影评分　t_rating.rate
2、核心SQL：三表联合查询，按照性别过滤条件，电影名作为分组条件，影评分作为排序条件进行查询
（2）完整SQL：
女性当中评分最高的10部电影（性别，电影名，影评分）评论次数大于等于50次

create table female10 as 
select "F" as sex, c.moviename as name, avg(a.rate) as avgrate, count(c.moviename) as total  
from t_rating a 
join t_user b on a.userid=b.userid 
join t_movie c on a.movieid=c.movieid 
where b.sex="F" 
group by c.moviename 
having total >= 50
order by avgrate desc 
limit 10;

男性当中评分最高的10部电影（性别，电影名，影评分）评论次数大于等于50次

create table male10 as 
select "M" as sex, c.moviename as name, avg(a.rate) as avgrate, count(c.moviename) as total  
from t_rating a 
join t_user b on a.userid=b.userid 
join t_movie c on a.movieid=c.movieid 
where b.sex="M" 
group by c.moviename 
having total >= 50
order by avgrate desc 
limit 10;

### 求movieid = 2116这部电影各年龄段

（因为年龄就只有7个，就按这个7个分就好了）的平均影评（年龄段，影评分）
（1）分析思路：

1、需求字段：年龄段t_user.age \#影评分　t_rating.rate
2、核心SQL：t_user和t_rating表进行联合查询，用movieid=2116作为过滤条件，用年龄段作为分组条件
（2）完整SQL：

create table answer4 as 
select a.age as age, avg(b.rate) as avgrate 
from t_user a join t_rating b on a.userid=b.userid 
where b.movieid=2116 
group by a.age;

### 该影评库中各种类型电影中评价最高的5部电影（类型，电影名，平均影评分）

（1）分析思路：

需求字段：电影id　movieid
电影名　moviename
影评分　rate（排序条件）
电影类型　type（分组条件）
2、核心SQL：

A.

需要电影类型，所有需要将answer7_A中的type字段进行裂变，将结果保存到answer8_A中
需要查询的字段：电影id　answer7_A.id
电影名　answer7_A.name（包含年份）
上映年份　answer7_A.years
影评分　answer7_A.rate
电影类型　answer7_A.movietype
B.

求TopN，按照type分组，需要添加一列来记录每组的顺序，将结果保存到answer8_B中
row_number() ：用来生成 num字段的值
distribute by movietype ：按照type进行分组
sort by avgrate desc ：每组数据按照rate排降序
num：新列， 值就是每一条记录在每一组中按照排序规则计算出来的排序值
C.　　从answer8_B中取出num列序号<=5的

（2）完整SQL：
A.　　需要电影类型，所有需要将answer7_A中的type字段进行裂变，将结果保存到answer8_A中

create table answer8_A as 
select a.id as id, a.name as name, a.years as years, a.rate as rate, tv.type as type 
from answer7_A a 
lateral view explode(split(a.type,"\\|")) tv as type;
