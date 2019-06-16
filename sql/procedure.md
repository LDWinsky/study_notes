# sql 函数 procedure

## 创建

```sql
  create procedure procedure_name()
  begin
    select * from customers;
  end;
```

* 注： 在sql命令行输入的时候会因为；判断输入结束而导致报错；可以临时的将结束符改为别的； `DELIMITER //` 创建完再改回。

## 参数

是函数就会有参数，sql的参数分为两种 in out; in 跟普通函数一样，传到函数体重作为变量使用。out 相当于普通函数的return 的作用，就是将生成的结果报错到参数中，可以在调动玩函数后使用。

### in 参数

```sql
  create procedure inargstest(
    in argsname type
  )
  begin
    select argsname from tableA;
  end;  
```

### out 参数

```sql
  create procedure out_arg_test(
    out arg_name type
  )
  begin
    select * from table into arg_name;
  end;
```

## 调用函数

```sql
  call out_arg_test(2000, @outvar)
  /* 2000 为 in 参数所以传整数， outvar 为 out 参数所以传 @outvar */
```

## 复杂函数

既然是函数当然需要有赋值，判断，声明变量等逻辑啦

### 声明变脸

```sql
  declare var int [default 3];
```

### 赋值

```sql
  select var1 into var2
```

### 判断

```sql
  if condition then
  end if;
```

