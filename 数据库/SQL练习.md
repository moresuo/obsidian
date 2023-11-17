***
<a name="C4oqh"></a>
### 前提工作
**创建表，插入数据**
```sql
-- 学生表
CREATE TABLE `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);
-- 课程表
CREATE TABLE `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);
-- 教师表
CREATE TABLE `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);
-- 成绩表
CREATE TABLE `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);
-- 插入学生表测试数据
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');
-- 课程表测试数据
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

-- 教师表测试数据
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

-- 成绩表测试数据
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```
<a name="X7nfT"></a>
### **1.查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号（重点**）
```sql
select a.s_id,a.s_score,b.s_score from
(select s_id,s_score  from score where c_id='01') a 
join (select s_id,s_score from score where c_id='02') b
on a.s_id=b.s_id
where a.s_score>b.s_score
```
<a name="yH6Yo"></a>
### 2、查询平均成绩大于60分的学生的学号和平均成绩（简单，第二道重点）
```sql
select s_id,avg(s_score) as '平均成绩' from score 
group by s_id 
having avg(s_score)>60
```
<a name="JdiAD"></a>
### 3、查询所有学生的学号、姓名、选课数、总成绩（不重要）
```sql
select s.s_id,s.s_name,count(sc.c_id) as '选课数',
sum(case when sc.s_score is NULL then 0 else sc.s_score end) as '总成绩' from student s 
left join score sc
on s.s_id=sc.s_id
group by s.s_id
```
<a name="xxaMV"></a>
### 4、查询姓“猴”的老师的个数（不重要）
```sql
select count(t_id) from teacher 
where t_name like '猴%' #名字可能重复，多个名字重复就会只算一个
```
<a name="NmNDa"></a>
### 5、查询没学过“张三”老师课的学生的学号、姓名（重点）
```sql
select s.s_id,s.s_name from student s 
where s.s_id 
not in(select sc.s_id from score sc join course c on sc.c_id=c.c_id
join teacher t on c.t_id=t.t_id
where t.t_name='张三')
```
<a name="TSV1d"></a>
### 6、查询学过“张三”老师所教的所有课的同学的学号、姓名（重点）
```sql
select s.s_id,s.s_name from
student s where s.s_id in(
select sc.s_id
from score sc join course c on sc.c_id=c.c_id
join teacher t on t.t_id=c.t_id
where t.t_name='张三')
```
<a name="azrTn"></a>
### 7、查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号、姓名（重点）
```sql
select s.s_id,s.s_name from student s
where s.s_id in(
select a.s_id from (
select s_id from score where c_id='01') a
join (
select s_id from score where c_id='02') b 
on a.s_id=b.s_id)
```
<a name="uNmOl"></a>
### 8、查询课程编号为“02”的总成绩（不重点）
```sql
select sum(s_score) from score where c_id='02'
```
<a name="FB4xB"></a>
### 9、查询所有课程成绩小于60分的学生的学号、姓名
```sql
select s.s_id,s.s_name from student s 
join (select s_id, max(s_score) from score group by s_id having max(s_score)<60) a
on s.s_id=a.s_id
```
<a name="LMX0i"></a>
### 10、查询没有学全所有课的学生的学号、姓名(重点)
```sql
select s.s_id,s.s_name from student s
join score sc on s.s_id=sc.s_id 
group by s.s_id 
having count(sc.c_id)<(select count(distinct c_id) from course)
```
<a name="OdSYv"></a>
### 11、查询至少有一门课与学号为“01”的学生所学课程相同的学生的学号和姓名（重点）
```sql
select distinct s.s_id,s.s_name from student s
join score sc 
on s.s_id=sc.s_id
where sc.c_id in(select c_id from score where s_id='01') and s.s_id!='01'
```
<a name="hoO1s"></a>
### 12、查询和“01”号同学所学课程完全相同的其他同学的学号(重点)
```sql
select sc.s_id from score sc 
join (select c_id from score where s_id='01') a
on sc.c_id=a.c_id group by sc.s_id
having count(distinct sc.c_id)=(select count(c_id) from score where s_id='01') and sc.s_id!='01'
```
<a name="TkRIC"></a>
### 13、查询没学过"张三"老师讲授的任一门课程的学生姓名和 [[SQL练习#^d3e974|44题]]一样（重点，能做出来）

^424b58

```sql
select s.s_name from student s
where s.s_id not in(
select sc.s_id from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id
join teacher t on c.t_id=t.t_id
where t.t_name='张三')

```
<a name="xdTns"></a>
### 14、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩（重点）
```sql
select s.s_id,s.s_name,(select avg(s_score) from score where s_id=s.s_id group by s_id) '平均成绩' 
from student s
where s.s_id in(
select s_id from score 
where s_score<60
group by s_id
having count(c_id)>=2)
```
<a name="iT2xr"></a>
### 15、检索"01"课程分数小于60，按分数降序排列的学生信息（和 [[SQL练习#^19c5f9|32题]] 重复，不重点）
```sql
select  s.* from student s 
join score sc 
on s.s_id=sc.s_id
where sc.s_score<60 and sc.c_id='01'
order by sc.s_score desc
```
<a name="nYTea"></a>
### 16、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩(重重点与 [[SQL练习#^c646b5|33]] 一样)
```sql
select s_id,
max(case when c_id='01' then s_score else null end) '语文',
max(case when c_id='02' then s_score else null end) '数学',
max(case when c_id='03' then s_score else null end) '英语',
avg(s_score) from score 
group by s_id
order by avg(s_score) desc
```
<a name="WjIjR"></a>
### 17、查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90 (超级重点)
```sql
select sc.c_id,c.c_name,max(sc.s_score) '最高分',min(sc.s_score) '最低分',
avg(sc.s_score) '平均分',
sum(case when sc.s_score>=60 then 1 else 0 end)/count(sc.s_id) '及格率',
sum(case when sc.s_score>=70 and sc.s_score<80 then 1 else 0 end)/count(sc.s_id) '中等率',
sum(case when sc.s_score>=80 and sc.s_score<90 then 1 else 0 end)/count(sc.s_id) '优良率',
sum(case when sc.s_score>=90 then 1 else 0 end)/count(sc.s_id) '优秀率'
from score sc
join course c
on sc.c_id=c.c_id
group by c.c_id
```
<a name="BPPrU"></a>
### 18、按各科成绩进行排序，并显示排名(重点row_number)

- <mark style="background: #D2B3FFA6;"><mark style="background: #D2B3FFA6;"> row_number over(partition by 分组 order by 排序)：无重复排序，1234</mark></mark>
- <mark style="background: #D2B3FFA6;">dense_rank over(partition by 分组 order by 排序)：连续排序，1223</mark>
- <mark style="background: #D2B3FFA6;">rank over(partition by 分组 order by 排序)：跳跃式排序，1224</mark>
```sql
select s_id,c_id,s_score,
row_number() over(partition by c_id order by s_score desc) '排名' from score
```
<a name="ggJBF"></a>
### 19、查询学生的总成绩并进行排名（不重点）
```sql
select s_id,sum(s_score) '总成绩' from score
group by s_id
order by sum(s_score) desc
```
<a name="iqR61"></a>
### 20、查询不同老师所教不同课程平均分从高到低显示(不重点)
```sql
select t.t_id,t.t_name,avg(sc.s_score) '平均分' from score sc
join course c on sc.c_id=c.c_id
join teacher t on c.t_id=t.t_id
group by t.t_id,t.t_name
order by avg(sc.s_score) desc
```
<a name="UqOCe"></a>
### 21、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩（重要 25 类似）

^227d51

```sql
select * from (
select s.*,sc.c_id,sc.s_score,
row_number() over(partition by sc.c_id order by sc.s_score desc) m
from student s join score sc 
on s.s_id=sc.s_id) a
where m in(2,3)
```
<a name="y6CzF"></a>
### 22、使用分段[100-85],[85-70],[70-60],[<60]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称(重点和18题类似)
```sql
select sc.c_id,c.c_name,
sum(case when sc.s_score<60 then 1 else 0 end) '[<60]',
sum(case when sc.s_score>=60 and sc.s_score<70 then 1 else 0 end) '[60-70]',
sum(case when sc.s_score>=70 and sc.s_score<85 then 1 else 0 end) '[70-85]',
sum(case when sc.s_score>=85 and sc.s_score<100 then 1 else 0 end) '[85-100]'
from score sc join course c
on sc.c_id=c.c_id
group by sc.c_id
```
<a name="VBOBT"></a>
### 23、 查询学生平均成绩及其名次（同19题，重点）
```sql
select sc.s_id,avg(sc.s_score),row_number() over(order by avg(sc.s_score) desc) '名次'
from score sc group by sc.s_id
```
<a name="tarwv"></a>
### 24、查询各科成绩前三名的记录（不考虑成绩并列情况）（重点与22题类似）

^a13633

```sql
select * from (
select s.*,sc.c_id,sc.s_score,
row_number() over(partition by sc.c_id order by sc.s_score desc) m
from student s join score sc
on s.s_id=sc.s_id) a
where m in(1,2,3)
```
<a name="UUora"></a>
### 25、查询每门课程被选修的学生数(不重点)
```sql
select c_id,count(s_id) from score
group by c_id
```
<a name="UWL3b"></a>
### 26、查询出只有两门课程的全部学生的学号和姓名(不重点)
```sql
select s.s_id,s.s_name from student s
join score sc on s.s_id=sc.s_id
group by s.s_id
having count(s.s_id)=2
```
<a name="HdsLR"></a>
### 27、查询男生、女生人数(不重点)
```sql
select s_sex,count(s_id) from student
group by s_sex
```
<a name="mkn0G"></a>
### 28、查询名字中含有"风"字的学生信息（不重点）
```sql
select * from student where s_name like '%风%'
```
<a name="QPWph"></a>
### 29、查询1990年出生的学生名单（重点year）
```sql
select * from student
where YEAR(s_birth)='1990'
```
<a name="dZOgk"></a>
### 30、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩（不重要）
```sql
select s.s_id,s.s_name,avg(sc.s_score) from student s
join score sc 
on s.s_id=sc.s_id
group by s.s_id
having avg(sc.s_score)>=85
```

### 31、查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列（不重要
```sql
select c_id,avg(s_score) from score 
group by c_id
order by avg(s_score),c_id desc
```

### 32、查询课程名称为"数学"，且分数低于 60 的学生姓名和分数（不重点）

^19c5f9

```sql
select c.c_name,sc.s_score,s.s_name from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id
where c.c_name='数学' and sc.s_score<60
```


### 33、查询所有学生的课程及分数情况（重点）
^c646b5

```sql
select s.s_id,s.s_name,
max(case when c.c_name='语文' then sc.s_score else null end) '语文',
max(case when c.c_name='数学' then sc.s_score else null end) '数学',
max(case when c.c_name='英语' then sc.s_score else null end) '英语'
from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id
group by s.s_id
```

### 34、查询课程成绩在 70 分以上的姓名、课程名称和分数（重点）
```sql
select s.s_name,c.c_name,sc.s_score from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id
where sc.s_score>70
```
### 35、查询不及格的课程及学生信息并按课程号从大到小排列 (不重点)
```sql
select s.s_id,s.s_name,sc.s_score,c.c_name,sc.c_id from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id   
where sc.s_score<60
order by sc.c_id desc

```
### 36、查询课程编号为 03 且课程成绩在 80 分以上的学生的学号和姓名（不重要）
```sql
select s.s_id,s.s_name from student s
join score sc on s.s_id=sc.s_id
where sc.c_id='03' and sc.s_score>80
```

### 37、求每门课程的学生人数（不重要）
```sql
select c_id,count(s_id) '人数' from score
group by c_id
```

### 38、查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩（重要 top）
```sql
select s.s_name,sc.s_score from student s
join score sc on s.s_id=sc.s_id
join course c on sc.c_id=c.c_id
where c.t_id in(select t_id from teacher where t_name='张三')
order by sc.s_score desc
limit 0,1
```
### 39、查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 （重点）
```sql
select distinct sc.s_id,sc.c_id,sc.s_score from score sc,
score a
where sc.c_id!=a.c_id and sc.s_id=a.s_id and sc.s_score=a.s_score
```
### 40、查询每门课程成绩最好的前两名（同 [[SQL练习#^227d51|21]] 和 [[SQL练习#^a13633|24]] 题）
```sql
select * from (
select sc.c_id,s.s_id,s.s_name,
row_number() over(partition by sc.c_id order by sc.s_score desc) m
from student s
join score sc on s.s_id=sc.s_id) a
where m in(1,2)
```


### 41、统计每门课程的学生选修人数（超过 5 人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列（不重要）
```sql
select c_id,count(s_id) '选修人数' from score 
group by c_id 
having count(s_id)>5
order by count(s_id) desc,c_id
```
### 42、检索至少选修两门课程的学生学号（不重要）
```sql
select s_id from score group by s_id
having count(c_id)>=2
```
### 43、查询选修了全部课程的学生信息（重点划红线地方）
```sql
select s.s_id,s.s_name,count(sc.c_id) from student s
join score sc on s.s_id=sc.s_id
group by s.s_id,s.s_name
having count(sc.c_id)=(select count(*) from course)
```
### 44、查询没学过“张三”老师讲授的任一门课程的学生姓名（还可以，自己写的，答案中没有）[[SQL练习#^424b58|13题同]]

^d3e974

```sql
select s.s_id,s.s_name from student s
where s.s_id not in(
select sc.s_id from score sc
join course c on sc.c_id=c.c_id
join teacher t on c.t_id=t.t_id
where t.t_name='张三'
)
```
### 45、查询两门及以上不及格课程的同学的学号及其平均成绩（还可以，自己写的，答案中没有）
```sql
select s_id,avg(s_score) from score
where s_score<60
group by s_id
having count(c_id)>=2
```
### 46、查询各学生的年龄（精确到月份）
```sql
select s_name,s_birth,TIMESTAMPDIFF(YEAR,s_birth,date(now())) '年龄'
from student
```
### 47、查询下周过生日的学生（无法使用 week、date (now ()）
```sql
select * from student
where week(s_birth)=week(date(now()),1)+1
```
### 48、查询本月过生的学生
```sql
select * from student
where MONTH(date(now()))=MONTH(s_birth)
```
### 49、查询下个月过生的学生
```sql
select * from student 
where month(s_birth)=mod(month(date(now())),12)+1
```
### 50、查询本周过生的学生
```sql
select * from student 
where week(s_birth,1)=week(date(now()),1)
```
