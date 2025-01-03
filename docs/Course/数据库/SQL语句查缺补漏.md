# SQL

注意sql中不能直接用减法符号代替集合差，要用EXCEPT

例：

```sql
(select ID, name from student) 
EXCEPT
(select takes.ID, student.name from takes join student on takes.ID = student.ID
where takes.year < 2017);
```

查询嵌套的时候，不能`select from (select ... from ... )`，要给内层表一个别名，否则会有问题，例子如下

```sql
SELECT MIN(max_salary)
FROM (
    SELECT depart_name, MAX(salary) AS max_salary
    FROM instructor
    GROUP BY depart_name
) AS department_salaries;
```

去重使用: `DISTINCT`

`UNIQUE` 相对的，更加用于定义约束，即create表的时候用的比较多。

`ALL` 用于比较一个值是否满足与子查询结果中所有值的某种关系（如大于、小于、等于等）。同理，可以用`SOME`来标识存在一个满足条件的值。

```sql
SELECT salesperson, amount
FROM sales
WHERE amount > ALL (
    SELECT amount
    FROM sales
    WHERE salesperson <> 'Alice'
);
```

子查询返回除 Alice 之外的所有销售员的销售额。

外层查询返回 Alice 的销售额是否大于所有这些值。

子查询：如果子查询中有多列记得用括号括起来：

```sql
select ID from customer
where (customer_street, customer_city)
in (
select customer_street, customer_city
from customer where 
customer_name = '12345')
EXCEPT 
select ID 
from customer where 
customer_name = '12345'
```

外键后面的on delete cascade表示删除主键时，外键也会被删除。

- 如果是on delete set null，父表记录被删除，子表所有对应元组的外键列会被设为NULL
- 如果是on delete restrict，在子表存在引用时，**禁止**删除


外键和普通约束完整性的区别是：**外键要求被引用的键一定要是主码**，而普通约束完整性可以自定义，只要保持唯一性

视图：

```sql
create view tot_credits(year, num_credits) as (
select ...
from table 
)
```

**不是所有视图都可以更新的，不能更新的视图只能做查询。**

可更新视图：基于**单个表**，且**不能包含聚合函数，DISTINCT，窗口函数，子查询，集合操作**。

`USING` 语法指定join中同名列的名称。

可以使用`alter table table-name add constraint`增加完整性约束，单个关系的约束有not null/unique/check(<谓词>)

- 插入和删除

```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);

DELETE FROM table_name
WHERE condition;
```

- 索引

```sql
CREATE INDEX index_name on 关系(属性);
```

- 授权

```
grant xxx
on 关系/视图
to 用户/角色列表
```

对应的有rovoke

- 触发器

语法：

```sql
create trigger trigger_name before/after/instead of update xxx of TABLE
referencing new row as nrow
referencing old row as orow
for each row
when xxx
begin 
...
end
```

注意`instead of`操作比较特殊，在事件发生前执行，并替代原始操作。

## 重点

基本查询

- SELECT - FROM -WHERE
- 表连接（INNER JOIN / OUTER JOIN），别名（AS），排序（ORDER BY xxx ASC/DESC）
- 聚合与分组（GROUP BY 和 HAVING）
- 子查询和集合操作：IN EXISTS ANY/ALL
- UNION / INTERSECT（交集） / EXCEPT

高级查询

- 视图，定义，更新限制
- 完整性约束（PRIMARY KEY、FORGIEN KEY（ON DELETE CASCADE / SET NULL / RESTRICT）
- 触发器

