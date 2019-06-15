# 术语

## 数据库

全部表的集合容器

## 表

根据一定类比关系组合起来的记录

* 尽可能的关系单一，比如客户的表格只用来记录其姓名的信息，不记录其使用的产品
* 表名唯一性

## 列

表格一列，记录一个属性，比如客户的名字。

* 其数据类型要相同。

## 行

带边一个对象。

## 主键

行中能代表自身的识别符，可以是一个属性，也可以是多个属性的组合。

* 唯一性
* 必须性

## SQL

一个操作数据的语法。开发者🐂🍺

* 特性：简单，功能强，多平台支持

# MYSQL

一个管理数据的库的软件。

## 安装（In MAC）

https://dev.mysql.com/doc/refman/8.0/en/osx-installation-pkg.html

## 打开 Mysql Client

/usr/local/mysql/bin/mysql -u root -p
输入 MYSQL 密码

## 引入测试用表

`引入数据，方便学习使用`

[测试用表](https://forta.com/wp-content/uploads/books/0672327120/mysql_scripts.zip)

```sql

create database crashcourse;

use crashcourse;

source ~/[filepath]/create.sql

source ~/[filepath]/populate.sql

```

# SQL 语法

## 基础查询 

### select from

语法: select col_name_in_table from table

在 table 中选择 col_name 列的数据;

* 注：select 后的列名可以是多个 select col1,col2 from table;
* DISTINCT 可以单一列出出现过的列名，但是不能使用多个。select distinct col from table;

### order by

对选取出来的数据进行排序。

```sql
  select *
  from table
  order by col1 [DESC], col2; // 先按照col1进行排序，col1相同再按col2
```

* 可以使用 DESC 来进行反序

### where

```sql
  select * 
  from table
  where condition
```

condition 为过滤的条件

数字过滤可选值： > , <, >=, <=, !=， =

```sql
  select *
  from table
  where age [>| <| != |] 1
```

字符过滤可选值：=，正则

= 就是字符串完全相同

正则分为两类

大众语法的正则表达式，拿JS来类比：a REGEXP abc === a.match(/abc/);

SQL 语法正则 a % b , a _ b; %: 等价于 .*? , _ 等价于 .;

```sql
    select *
  from table
  where age REGEXP tes{1,4} /* 可以匹配 age === tes111 */
```

范围过滤可选值： between x and y； key in (x, y)； key not in (x, y)；

between 1 and 3; 匹配 1到3 的值；
key in (1， 3)； 匹配 1 或者 3;
key not in (1, 3); 匹配不是1和3的值；

特别的 a is null; 匹配a不是空的行；

拼接简单过滤条件：
上面的简单过滤条件可以使用AND 或者 OR进行组合, 多使用（）来消除歧义；

```sql
  select * from orders
  where (orders.a = 3 AND order.b = 4) OR order.c = 5
```

### 自定义过滤出来的数据跟重命名

当我们需要对选出来的数据，进行从命名的时候，比如 select first_name, last_name form customs; 会生成 [{first_name: 'haha', last_name: 'hehe'}]; 但是我们合起来生成 [{name: 'haha hehe'}] 的使用就可以使用 concat as 的语法；

```sql
  select concat(first_name, ' ', last_name) as name
  from customs;
```

### 汇总函数

有时需要对获得的列进行汇总进行计算，比如计算一列的平均值。这是就需要SQL的提供的汇总类函数。

```sql
  select avg(price) from orders where custom_id = 'test';
  /* 计算出 custom_id 为 test 全部列 price 的平均值 */
```

因为使用了汇总函数的原因 select 后的列字段不能出现单个的列名，比如 select price, avg(price) 会报错；

其他的汇总函数大概有， count, max, min, sum 等

### 分组

有时候我们想将一个表格分类之后在汇总，比如像得到班上男生和女生的平均分表格，这是用单一使用汇总就不是很方便，这时候就可以先使用 group by 分组之后再汇总。

```sql
  select sex, avg(score) from classA group by sex;
```

有时候我们需要对分组后的组进行过滤，比如班上有不同的兴趣班，我们想列出兴趣班人数大于5的组。这时候使用where来过滤好像就不靠谱了。这时候就需要having来过滤。

```sql
  select club, count(*) from classA group by club having count(*) > 5;
```

### limit

当数据出来的时候可能有很多，我们可以用limit来去除部分想要的行。最后使用 limit [startrow,] rowcount;

```sql
  select * from classA limit 0, 3
```

### 顺序

目前已经将大部分的简单过滤语法学完了，这里需要总结下语法出现的顺序；

select > from > where > group by > having > order by > limit

注：到这里基本上只在一个表中的操作基本就完了

# 多表操作

## 子查询语句

上面的 select ... from 语句叫做是一行query； 我们可以在一行query中插入另一行query这个就叫做子查询；

一般又两个用途，一个是在另一个表中获取数据，用于当另一个表的查询条件。另一个是用当前表的数据当做另一个表的查询条件来查询完再拿回来当当前表的新列输出。

```sql
  /* 用途一： */
  select * from classA
  where parent in (select parent_id from parentTable where parent_id === 'ahole')
  /* 找出班级里爸爸是ahole的学生，ahole真能生啊 */
```

```sql
  /* 用途二 */
  select *, (
    select count(*) from parent
    where classA.parent = parent.id
  ) from classA
```

* 注：观察可发现子查询输出的格式要么是数组要么是单一的值

## 表联结

关系型数据库的一个特点就是一个表含有的信息会尽可能的单一，比如产品的表就只会对产品进行描述，不会对其产商进行细节描述，只会对其产商提供一个id，已对接产商表的产商。这个id就是*外键*。如果我们要生成一个表格，带上生产商的信息，只给个ID，鬼啃得懂。这时候可以使用子查询但是，太慢了。这是就需要*表联结*。

### 生成联结表

```sql
  select * from tableA, tableB
  where tableA.id = tableB.id;
```

* 解析：where table.id = tableB.id; from tableA, tableB; 这里会将两个表各行进行一个笛卡尔积；生成 tableA 行 * tableB 行的函数，而且大部分都不是我们要的，这里使用 table.id = tableB.id 来过滤就能生成两个表中相同 id 的行，形成两个表对应信息的联结

### 不同的联结方式

自联结：表跟自身联结。
一般用于通过一个col获取那一行的另一个属性，然后在通过这个属性来获取相同的行。如：我们发现一个员工老是摸鱼，我们想知道跟这个员工一样来自咸鱼镇的员工是不是也老师摸鱼。

外部联结：
一般场景我们使用普通的联结就能得到想要的结果，比如我们有两个表，一个记录产品的销量，另一个记录其产商，我们想的到一个产品销量跟其产商的联结表。但是如果我们的销量惨淡，只有一个产品卖出去，那么我们用普通的联结就会出现，只有一行的情况。如果我们想把没有销量的产品也列出来呢？就需要外部联结`outer join on`啦 。

```sql
  select * from order right outer join vendor on order.prod_id = vender.prod_id;
```

* left or right ; 翻译为哪边加入哪边，left 就是左边加入。右边表格数据加入左边数据，左边出现没有被匹配到的就把值填为Null；

## 全文搜索

全文搜索视为了弥补我们自己写正则遇到的问题。一，太难写。二，性能差。三，不能根据匹配程度返回。（只有本数据表格式支持全文搜索。比如MyISAM）

为了使用全文搜索需要在建立表的时候指明那个字段支持。

```sql
  create table full_text_example (
    note_id init not null, auto_increment,
    note_text text null,
    primary key(note_id),
    FULLTEXT(note_text)
  ) engine=MyISAM;
```

### match against

```sql
  select * from full_text_example
  where match(note_text) against('rabbit');
```

该 query 就会返回带有rabbit的行了（根据匹配比重排序）。

* 到这里查询的都已经搞完了😆。

## 对表进行增删查改

### 创建

* 创建格式如下

```sql
  create table tableName (
    col_name type (not Null | Null) default defaultValue [auto_increment],
    col_name type (not Null | Null) default defaultValue [auto_increment],
    primary key (col_name),
    [FULLTEXT]
  ) ENGINE=(MyISAM, InnoDB, MEMORY)
```

ENGINE.memory 会把数据放在内存中。

### 添加列

```sql
  ALERT Table table_name ADD new_col_name type;
```

### 添加外键

```sql
  alert table table_name add constraint fk_name foreign key (col) references table (col)
```

### 删除表

```sql
  drop table table_name;
```

### 重命名

```
  rename table table_name to new_table_name
```

### 插入行

```sql
  insert into table_name (
    columns,
    columns,
    columns,
    columns,
  ) values (
    value,
    value,
    value,
    value,
  ), (
    value,
    value,
    value,
    value,
  )
```

```sql
  insert into table_name (
    columns,
    columns,
    columns,
    columns,
  ) select columns, columns,columns,columns from table_name2
```

### 更改行

```sql
  update table_name
  set columns = 'new date'
  where columns = 'old date'
```

### 删除行

```sql
  delete from table_name
  where columns = 'new date'
```

## 视图

用于保存query的方法，当我们有一个比较复杂的query的时候，不想每次都重新写的就可以保存起来当成一个table去用。可以跟新但是不建议。

```sql
  create view view_name as select * from query where query.id = 33
```
