# 触发器

一个可以在数据库增删改是自动触发的逻辑，简直不能太有用。

* 触发器分为 (update, delete, insert) * (before, after) 的笛卡尔积种情况。
* 触发器只能在表中应用
* 每种触发器只能在一个表中定义一个，也就是最多6个
* before trigger 报错之后，后续的逻辑将被停止

## 语法

```sql
  create trigger trigger_name before|after update|delete|insert on tablename
  for each row donesomething
```

## update trigger

update 期间临时有两个表 NEW ，OLD。 NEW 表报错新的数据行，一般是在 before update 的时候对数据进行格式化。

```sql
  create trigger trigger_name before update on tablename
  for each row set NEW.column = Upper(NEW.column)
```

OLD 保存的时候原来的数据行，一般拿来备份。

### delete trigger

类似上，有一个OLD表，用于报错被删除的数据。一般结合before使用，用于备份数据。

```sql
  create trigger trigger_name before delete on tablename
  for each row 
  begin
    insert into table(xxx) value(NEW.column);
  end;
```

### insert trigger

有一个new表，保存插入的行。这里要注意的是，before时auto_increment 的列都是0. after之后才是其应该有的值。
