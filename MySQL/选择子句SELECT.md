## 选择语句SELECT

### 选择语句

从单个面板检索数据

```mysql
-- 第一步选择数据库，被选中的数据库会在schemas中以粗体显示
-- sql 不区分大小写
-- 一般sql关键字都大写，其他内容的都小写
-- 多个sql语句需要使用 分号; 分隔开
USE sql_store;

SELECT (列，属性) FROM Tables;
-- 例如 查询 customers 表格中的所有内容
SELECT * FROM customers;

SELECT *
FROM customers
WHERE customer_id = 1 -- 添加条件
ORDER BY first_name -- 以某一列排序
-- 子句的顺序很重要
```



---



### SELECT 子句

```mysql
# 使用 * 查询所有列
# SELECT 一般都要指定属性（列），
# 因为当列很大的时候就查询所有列会占用服务器带宽

# 显示结果的顺序与查询的顺序一致
SELECT last_name, first_name 
FROM customers

# 还可以在查询中加入算数表达式，
# 下式的结果即所有的points在原来结果上加10
SELECT 
	last_name, 
	first_name, 
	points + 10
FROM customers

# 给列取别名 AS
SELECT 
	last_name, 
	first_name, 
	points + 10 AS discount_factor
FROM customers

# 在别名中如果想要有 空格 需要使用 引号(单双引号均可) 将字符串包裹
points + 10 AS "discount factor"

# 去除重复的 DISTINCT
SELECT DISTINCT state
FROM customers
```





---

### WHERE 子句

```mysql
SELECT *
FROM Customers
-- 查询积分大于 3000 的用户
WHERE points > 3000

-- 一些运算符号
>
>=
<
<=
= -- 等号是这个
!= -- 不等于
<> -- 也是不等于

-- SQL里要给日期加上引号
-- 大于 1990-01-01
SELECT *
FROM Customers
WHERE birth_data > '1990-01-01'
```



### AND, OR, NOT

```mysql
AND -- 与 也可以是使用 &&
OR -- 或 ||
NOT -- 非
```



### IN

```mysql
-- 为了解决 
-- state = 'VA' OR state = 'GA' OR state = 'FL'
-- OR 只能连接表达式或者条件
-- 连续多个 OR 的问题
WHERE state IN ('VA', 'GA', 'FL')
WHERE state NOT IN ('VA', 'GA', 'FL')
```



### BETWEEN

```mysql
-- 查询介于1000和3000之间的
WHERE points >= 1000 AND points <= 3000
WHERE points BETWEEN 1000 AND 300
```



### LIKE

```mysql
-- 用于匹配字符串用的
-- % 匹配 任意数量长度的字符

-- 匹配单个字符
-- _y
```



### REGEXP

```mysql
-- 查询包含某个字段的
SELECT *
FROM customers
WHERE last_name REGEXP 'field';

-- 匹配开头， ^
-- 匹配结尾， $
-- 或者 |； 'filed|mac|rose' 就是匹配名字里有 filed 或者mac 或者rose的
-- 字符集合， []，匹配放阔号内的任意一个字符
-- 负字符集，[^...] 匹配未包含的任意字符
```



### IS NULL

```mysql
-- 如果字段为空
WHERE phone IS NULL
-- 字段不为空
WHERE phone IS NOT NULL
```



### ORDER BY

```mysql
-- 为数据排序, 默认主键排序
ORDER BY first_name

-- 降序，则在之后加入 DESC
ORDER BY first_name DESC

-- 使用多列排序，先以某一列排序，在此基础上以另一列排序
ORDER BY state DESC, first_name

-- MySQL可以用 任何列排列数据，不管是不是在 SELECT 子句中
```



### LIMIT

```mysql
-- 限定查询返回的记录，例如只想要前三个
SELECT *
FROM customers
LIMIT 3

-- 添加偏移量，在网页分页的时候很有用
SELECT *
FROM custoemrs
LIMIT 6, 3 -- 6 为偏移量，3 为选择的数据量

```

