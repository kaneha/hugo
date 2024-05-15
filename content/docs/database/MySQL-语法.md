+++
title = 'MySQL - 语法'
date = 2024-05-13T15:11:31+08:00
weight = 10
+++



## 增删改查

### 基础语句

``` sql
-- 1. 增
INSERT INTO <table_name> (field_1, field_2...) VALUES(val_1, val_2...);
-- 一次插入多条
INSERT INTO <table_name> (field_1, field_2...) VALUES (val_1, val_2...), (val_1, val_2...);
-- 将查出的数据直接插入某表
INSERT INTO <table_name> (field_1, field_2...) SELECT (field_1, field_2...) FROM ...;
-- 将查出的数据直接插入某表 (要求表2不存在, mysql会自动创建目标表)
SELECT field_1, field_2... INTO <table_2> FROM <table_1>;

-- 2. 删
DELETE FROM <table_name> WHERE ...;
-- 质上是直接删表,再重新建表,而不是逐行删除
TRUNCATE <table_name>

-- 3. 改
UPDATE <table_name> SET X=X, Y=Y, Z=Z WHERE ...
-- 出错也不停止后续更新
UPDATE IGNORE <table_name> ...

-- 4. 查
SELECT <field_1>, <field_2>... FROM <table_name> WHERE <clause>
```

### WHERE子句

* BETWEEN : `...WHERE id BETWEEN 1 AND 10`
* IN : `...WHERE status IN(0, 2, 99)`, `...WHERE (id, score) IN(SELECT id, score FROM ...)`
* AND : `...WHERE id>10 AND price<500`
* OR : `WHERE id<100 OR id>999`
* NOT : 对判断句取反
* EXISTS : `WHERE EXISTS number=20`, `...WHERE EXISTS (SELECT ...)`
* LIKE : `...WHERE username LIKE 'my%'`
  * `%` : 表示0-n个字符
  * `_` : 表示1个字符
* REGEXP : `...WHERE username REGEXP '[aeiou]%'`



## 进阶查询



### 分组

写法: `... WHERE ... GROUP BY <字段1> ... <字段n> HAVING <查询条件>`

要注意的点:

* 关于查询字段: SELECT后只能写被分组的字段和聚合函数
  * `SELECT name, sum(score), count(subject) FROM student GROUP BY name; `
* 对多个字段分组: 按照先后顺序进行分组
  * `GROUP BY class, subject` : 先将每个年级分组, 再将每个年级内的科目分组.
* HAVING : HAVING的执行顺序在WHERE之后, 用于对分组结果进行过滤, 例如对学生总成绩进行分组时, 使用HAVING排除总分低于200分的学生.
  * `SELECT name, sum(score) AS total FROM student GROUP BY name HAVING total > 200;  `

### 排序

`ORDER BY <field_1> <asc/desc> ... <field_n> <asc/desc>`

例如: `SELECT * FROM student ORDER BY subject asc, score desc; `

### 去重

``` sql
-- 1. DISTINCT去重
-- distinct 作用于select后的所有字段, 只有全部字段都重复的条目才会被去重
SELECT DISTINCT name, role FROM users;
-- DISTINCT 关键字可以用在函数内
SELECT COUNT(DISTINCT score) FROM students;
-- distinct 必须作用于所有查询字段, 不能针对某个字段
-- 错误示例: XXX --> SELECT name, DISTINCT role FROM users;

-- 2. 注意⚠️: GROUP BY 也可以用来去重
```



### 限制条数/分页

限制输出条数, 也可以用作分页, 

* `LIMIT x` : 输出 x 行
* `LIMIT y, x` : 从第 y 行开始, 输出 x 行.
* `LIMIT x OFFSET y` : 从第 y 行开始, 输出 x 行.

> x从1开始计数,表示输出x行.
>
>  y从0开始计数, 表示从第y行开始输出.



### 多表查询/关联查询/连接查询

要注意的点:

* 连接查询使用ON作为连接条件, ON的执行顺序优先于WHERE.
* 如果两张表的连接字段字段名相同, 可以用 `USING(id)` 替换 `ON a.id=b.id`
* 内连接与左外连接的区别: 
  * 内连接查询的内容: 两张表on关联上的数据
  * 左连接查询的内容: 内连接的内容 + 无关联的左表数据 (也就是左表全部数据都被查询出了, 无关联左表数据的右表字段指为null)

* 多表关联时, 按顺序执行关联, 例如有学生表, 中间表和课程表. 先关联出学生表和中间表的数据, 再用此数据关联课程表, 最终查出学生的所有课程.

>  内连接的查询条数计算:
>
> * 自连接的情况
>   * 假设用学生成绩表自连接. 小明有3条成绩数据, 分别是语文数学和外语, 那么用name作为连接条件自连接时会查出几条数据? 答案是9条. a表3条数据中的每条数据都对应b表中的3条数据
> * 非自连接的情况
>   * 假设有超市表和商品表, 有2个超市, 一号超市有3种商品, 二号超市有5种商品, 那么会查出几条数据? 8条. 一号超市: 1 * 3 = 3 ; 二号超市: 1 * 5 = 5.
> * 总结: 一对多时, 每个一对应的多的条目数相加就是查出的总条数

**内连接** : 

``` sql
-- 写法1
-- 注意: 如果不写 WHERE子句,那么会查询到的行数会是表一的条目数*表二的条目数(笛卡尔积).
SELECT * FROM users u, roles r WHERE u.id = r.id

-- 写法2 (inner join 可简写作 join)
SELECT * 
FROM users u inner join roles r
ON u.id = r.id
```



**左外连接/右外连接** : 

``` sql
-- left outer join 简写作 left join.
SELECT u.name, r.role
FROM users u left join roles r
ON u.id = r.id
```

左连接时, 只查询左边的数据, 左边无右边有的数据不显示, 左边有右边无时, 右边的字段显示为null.

右连接与左连接相反.



### 组合查询/联合查询

组合查询用于将多个 select 语句组合, 并将结果作为单个查询结果集返回.

使用规则:

1. UNION 中的每个查询必须由两个及以上的 SELECT 语句组成
2. 多个 SELECT 语句之间查询字段个数必须相同.
3. SELECT 语句之间字段类型必须一一对应,但字段名可以不同(查询结果的列名 以第一条SELECT 语句为准)
4. UNION 会去除重复行, `UNION ALL` 不会去重

``` sql
SELECT name, class, subject, score FROM student
UNION
SELECT stu_name, class, subject, totle_score FROM stu_total_score
```



### 子查询

`子查询`也称为`内部查询`, 包含子查询的查询称为`外部查询`. 子查询可以在任何可以使用表达式的地方使用.

**SELECT后的子查询 :**

``` sql
-- SELECT后的子查询只能查询一个字段并且只能返回一条数据
-- 如果查询多个字段则会报错, 如果返回多条数据则查不到数据
SELECT 
    (SELECT DISTINCT salary 
    FROM employee 
    ORDER BY salary DESC LIMIT 1, 1
) AS SecondHighestSalary
```



**WHERE子句中的子查询 :**

``` sql
-- where子句中的子查询, 查询分数最高的学生的信息
SELECT name, class, score
FROM student
WHERE
	score = (
        SELECT MAX(score) FROM student
    )
	
```

**FROM后的子查询 :**

``` sql
-- FROM后的子查询
-- 注意: 
-- 	1. 子查询返回的结果集将用作临时表, 此表也称为派生表
-- 	2. FROM后的子查询必须起别名, 否则会报错
SELECT max(score), min(score), avg(score)
FROM (
	SELECT score FROM student
) temp
```



## 其他

### 锁

**悲观锁**

> SELECT * FROM table_name WHERE id=1 FOR UPDATE;

* 锁范围: where子句中如果指定了主键或者索引, 那么`for update`添加的是`行级锁`, 否则添加`表锁`.

* 释放锁: 提交事务或者回滚事务会释放锁. (mysql的innoDB默认开启自动提交, 所有写操作都会自动被提交, 要注意是否需要关闭自动提交)

**乐观锁**

1. 在表定义中加入version字段, 读取数据时把version字段一起读出
2. update数据时, 把version字段递增, 并且在where子句最后加上 version=之前读出的version
3. 如果update语句执行时数据的版本号和之前读取的不一致, 则更新失败(修改行数为0), 说明数据已被其他事务更新
4. 更新失败后可以自己决定处理策略(重新读取并更新之类的)
