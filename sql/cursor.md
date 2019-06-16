# 游标

游标：该概念比较抽象，可以抽象理解为每一次的查询都会增加一的一个数值。sql 开发的一个作用，当需要复杂情况需要对每次检索出来的行做一个复杂的操作的时候使用，所以非常适合在函数中进行操作。

## 语法

```sql
  /* 游标在MYSQL中只有在函数中才存在 */
  create procedure procedure_test()
  begin

    declare index cursor;
    for select order_num from orders;

    declare done boolean default 0;
    declare o int;

    /* 定义一个监听函数，当 sql 报出 '0200' 的时候 done 赋值为 1， '0200' 表示没有数据了 */
    declare continue handler for sqlstate '02000' set done = 1;

    /* 创建一个表格，如果没有这个表格的话 */
    create table if no exist order_nums(
      num int
    );

    /* 是有游标前需要开启 */
    open index;

    repeat

      fetch index into o

      insert into order_nums(
        num
      ) value (
        o
      )

    until done end repeat;

    /* 结束时删除游标清楚内存 */
    close index;

  end;

```

* 感觉没有什么卵用，起码对于我来说。对于自由开发者来说，就是爽，感觉不用，了解下就行了，当做一种知识，了解就过。