# SQL

[TOC]



### 缩进

```mysql
select
       id,
       user_name,
       sex
from c_user
    where id > 10
        and sex = '0'
    group by tel;
```

### 关键字大写

### CASE WHEN

```
分组、更新
```

### HAVING

```
独立使用
```

### 自连接（可重排列，排列，组合）

```mysql
#可重排列
SELECT
    P1. name AS name_1,
    P2. name AS name_2
FROM
    Products P2,
    Products P1;
#排列
SELECT
    P1. name AS name_1,
    P2. name AS name_2
FROM
    Products P2,
    Products P1
WHERE 
    P1.name <> P2.name;
#组合
SELECT
    P1. name AS name_1,
    P2. name AS name_2
FROM
    Products P2,
    Products P1
WHERE 
    P1.name > P2.name;
#局部不一致
SELECT DISTINCT
    p1. NAME,
    p1.price
FROM
    Products2 p1,
    Products2 p2
WHERE
    p1.price = p2.price
AND p1. NAME <> p2. NAME;

#删除重复
DELETE 
	FROM  c_user c1
	where id <
      (select max(c2.id)
      	from c_user c2
      		where  c1.user_name = c2.user_name
            		and c1.sex = c2.sex
       );
#排序
select id,
       user_name,
       sex,
       (select count(c2.sex) from c_user c2 where c2.sex > c1.sex) + 1 AS RANK_1
from c_user c1
order by RANK_1;

#排序
SELECT
    p1. NAME,
    p1.price,
    (
        SELECT
            COUNT(p2.price)
        FROM
            Products2 p2
        WHERE
            p2.price > p1.price
    ) + 1 AS rank
FROM
    Products2 p1
ORDER BY
    rank;
    
```

### COALESCE

```mysql
返回非空表达式
select id,COALESCE(address,'NNN') AS address from c_user;
```

## 优化

#### 参数子查询 EXISTS

```
exists instead of  in
exists 查到数据就终止，可以用到索引
IN 后面是子查询
SQL 会先执行 IN 后面的子查询，将子查询的结果保存在一张临时的工作表（内联视图），然后扫描整个视图
exists  不会产生临时表
```

#### 避免排序

```
产生排序
group by 
order by 
聚合函数（sum,count,avg,max,min）
distinct
集合运算符（union,intersect,except）
窗口函数（rank,row_number）
```

#### 集合运算符的All可选项

```
sql 中有 union,intersect,except 三个集合运算符
默认情况下，这些运算符会为了避免重复数据进行排序
union all 不排除重复数据，所以无需排序
```

#### exists 代表 distinct

```mysql
SELECT DISTINCT I.item_no
FROM Items I INNER JOIN SalesHistory SH
ON I. item_no = SH. item_no;
## exists better
SELECT item_no FROM Items I
WHERE EXISTS 
        (SELECT *
           FROM SalesHistory SH
          WHERE I.item_no = SH.item_no);
```

#### 极值函数中使用索引（MAX/MIN）

```
max/min 都会进行排序，如果参数字段上没加索引会导致全表扫描
```

#### 能写在 where 子句里的条件不要写在 having 子句里

```
group by  子句进行聚合时会进行排序，如果事先通过 where 子句 能筛选出一部分行，减轻排序负担
where 子句可以使用索引 having 子句针对聚合后生成的视频进行筛选
很多时候聚合后生成的视图并没有保留原表的索引结构
```

#### Group by order by 子句中使用索引

```
索引有序，排序本身都会被省略到
```

#### 使用索引，条件表达式左侧应该是原始字段

```
在索引上进行运算，对索引列使用函数，都无法用到索引，应该把列单独放在左侧
```

#### 尽量避免使用否定形式

```
<>
!=
not in 
都不能使用到索引，导致全表扫描
```

#### 进行默认的类型转换

```
× SELECT * FROM SomeTable WHERE col_1 = 10;
○ SELECT * FROM SomeTable WHERE col_1 = '10';
○ SELECT * FROM SomeTable WHERE col_1 = CAST(10, AS CHAR(2));
默认类型转换会增加额外的性能开销，还会导致索引不可用
```

#### 减少中间表

```
子查询的结果会产生一张新表，大量使用中间表会消耗内存资源，原始表中的索引不容易用到
```

#### 灵活使用 having

```mysql
SELECT *
  FROM (SELECT sale_date, MAX(quantity) AS max_qty
          FROM SalesHistory 
         GROUP BY sale_date) TMP
         WHERE max_qty >= 10;
 # 生成tmp 临时表
 SELECT sale_date, MAX(quantity) 
  FROM SalesHistory
 GROUP BY sale_date
HAVING MAX(quantity) >= 10;
```

**having 子句和聚合操作时同时执行的，比起中间表在执行 having 效率更高**

#### 多个字段使用 in 汇总一处

```mysql
SELECT id, state, city 
  FROM Addresses1 A1
 WHERE state IN (SELECT state
                   FROM Addresses2 A2
                  WHERE A1.id = A2.id) 
    AND city IN (SELECT city
                   FROM Addresses2 A2 
                  WHERE A1.id = A2.id);
## better 不用考虑关联性，没有中间表产生，只执行一次
SELECT *
  FROM Addresses1 A1
 WHERE id || state || city
 IN (SELECT id || state|| city
       FROM Addresses2 A2);
```

#### 延迟查询limit

```mysql
SELECT <cols> FROM profiles where sex='M' order by rating limit 100000, 10;
# limit offset rows 会扫描一整行数据 扫描 offset 遍
找到 offset 之后抛弃 offset 之前的数据，再开始读取 rows 行
#
SELECT <cols> 
  FROM profiles 
inner join
(SELECT id form FROM profiles where x.sex='M' order by rating limit 100000, 10)
as x using(id);
```

#### limit 1

#### 组合索引最左匹配原则

```
存在联合索引 条件的顺序 决定索引的命中
○ SELECT * FROM SomeTable WHERE col_1 = 10 AND col_2 = 100 AND col_3 = 500;
○ SELECT * FROM SomeTable WHERE col_1 = 10 AND col_2 = 100 ;
× SELECT * FROM SomeTable WHERE col_2 = 100 AND col_3 = 500 ;
```

#### Like 最左匹配原则

```
× SELECT * FROM SomeTable WHERE col_1 LIKE '%a';
× SELECT * FROM SomeTable WHERE col_1 LIKE '%a%';
○ SELECT * FROM SomeTable WHERE col_1 LIKE 'a%';
```

## Auto-Increament

```mysql
修改自增偏移
SELECT
	auto_increment
FROM
	information_schema.`TABLES`
WHERE
	table_name = 'cus_dict'
AND TABLE_SCHEMA = 'cus_product';

alter table cus_dict  AUTO_INCREMENT=16;
```

## 常用函数方法

### 切割

```mysql
增加前缀
update forumdata_userttt set userLink=concat('http://weibo.com/',userLink) where id>198;
去掉前缀
(从左数第二位之前的字符去掉；包括第二位)
update forumdata_userttt set userLink=right(userLink,length(userLink)-2) where id >= 1
```

### 递归查询

```
find_in_set（str,strlist）
str 代表要查询的字符串，strlist 是一个以逗号分隔的字符串 like（'a,b,c）
查找 str 字符串 在 strlist 中的位置，返回结果为 1～n ,没有找到则返回 0
eg :
select FIND_IN_SET('b','a,b,c,d'); 
select * from dept where FIND_IN_SET(id,'1000,1001,1002'); 
```

#### *concat*

```
1.concat 是以逗号为默认的分隔符
  select CONCAT('M','Y','S','Q','L') from dual; 
2.concat_ws 可以指定分隔符
  select CONCAT_WS('_',M','Y','S','Q','L') from dual; 
3.group_concat 特定分隔符拼接字符串，可以分组，列转行，去重【长度限制 1024，show variables like 'group_concat_max_len';】
  group_concat( [distinct] 要连接的字段 [order by 排序字段 asc/desc ] [separator '分隔符'] )
```

##### *group_concat 不支持 limit,加上子查询可以实现 limt*

### New table

```
create table position
(
    id     bigint primary key ,
    name   varchar(20),
    salary varchar(20),
    city   varchar(10)
) engine InnoDB
  default CHARACTER set utf8mb4
  collate utf8mb4_general_ci;
```

