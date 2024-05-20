+++
title = 'MySQL - 函数'
date = 2024-05-13T17:08:53+08:00
weight = 10
+++

施工中...

## 函数

### 聚合函数

要注意的点:

* DISTINCT关键字可以与聚合函数一起用,例如: `count(distinct score)`

#### count()

#### sum()

#### avg()

#### max()

#### min()

### 日期函数

#### datediff()

`datediff(date1, date2)` : 第一个日期减去第二个日期, 注意区分date和datetime

``` sql
-- 相当于 date1 - date2 = 1
where datediff(date1, date2) = 1
```



### 数学计算函数

#### round()

`round(val [, 小数位])`

四舍五入, 可以指定保留几位小数, 不传小数位时默认取整数

``` sql
-- 查询学生成绩优秀率, 保留2位小数
select
    round(
	    count( if(s.score > 80, 1, null) ) / count(1) , 2
	) as '优秀率'
from student s;
```



### 其他函数

#### if()

`if(express, trueVal, falseVal)`

if() 函数用于根据条件返回值, 表达式为 true 返回 trueVal, 表达式为 false 返回 falseVal.

``` sql
-- 查询学生成绩及格率
-- 问: if函数中的 falseVal 为什么是null不是0?
-- 		因为count会把0统计进去, 但是null不会被统计
select
	count( if(s.score > 60, 1, null) ) / count(s.score)  as '及格率'
from student s;
```



#### concat()

`concat(str1, ... strN)`

拼接字符串

``` sql
select 
	concat(class, '-', name) as class_name,
	subject, score
from student;
```



#### find_in_set()

`find_in_set(str, strlist)`

strlist是用逗号分隔的字符串, 注意分隔中不能有空格, find_in_set()不是模糊查询

``` sql
-- labels字段示例: '贫穷,男性,90后'
-- 查询带有 贫穷 标签的用户
SELECT name, labels 
FROM customers 
WHERE FIND_IN_SET('贫穷', labels);
```



## 窗口函数

mysql8开始支持窗口函数

要注意的点:

* 窗口函数写法: `窗口函数() OVER (partition by 列名 order by 列名) `

* 窗口函数写在什么位置: `SELECT后`
* 窗口函数可以写哪些函数: 
  * `专用窗口函数` : 例如 row_number() 等
  *  `聚合函数`
* 窗口函数特性:
  * `OVER` 关键字表示开窗, 窗口内可以对查询出的数据分区或排序
  * `partition by` 表示分区, 它不会影响条数, 只是单纯的分区. 例如 `partition by subject` 会将相同subject的数据放到一起
  * 窗口内的 `partition by` 和 `order by`  都只是影响数据的排序方式, 如果窗口外有`order by`, 则最终返回数据按照窗口外`ORDER BY`指定的顺序排列, 窗口内的排序只影响窗口函数的计算; 如果窗口外没有`order by`, 则最终返回数据使用窗口函数的排序
  * 窗口内可以单独使用 `partition by` 和 `order by`, 也可以两个一起用



### row_number()

添加一列用作行号(row_number)

``` sql
-- 注意: row_number()函数后记得用as给列起名
SELECT 
    row_number() OVER (ORDER BY score desc) as row_no,
    subject, name, score
FROM student;
```

### rank()

添加一列用作排名, 但是如果有并列第1, 则没有第2名, 第1名后面就是第3名.

写法与row_number()完全相同

### dense_rank()

添加一列用作排名, 与rank()不同的是不会跳过名次, 有并列第1时, 第1名后面仍是第2名.

写法与row_number()完全相同



### lag()

写法: `lag(列名, offset, defaultVal) over ...`

用于获取窗口内指定列往上数第offset行的列值, 没有值时使用默认值defaultVal.

参数可以只指定列名, offset默认为1, defaultVal为null, 例如: `lag(列名)`

```sql
-- 假设有一张表名为ids的表只有一个id字段. 有3条数据分别为 1, 2, 3
SELECT 
	id,
	lag(id, 1, 999) OVER (ORDER BY id) as lag_value
FROM ids;
-- 查询出的数据为
-- id	lag_value
-- 1	999
-- 2	1
-- 3	2
```



### lead()

写法: `lead(列名, offset, defaultVal) over ...`

与lag()相反, lead()是往下数
