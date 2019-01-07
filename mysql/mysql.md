1. 一张表中只能定义一个主键，却可以定义多个唯一性约束！
2. 主键列不允许存放NULL值，而普通的唯一性约束列可以存放NULL值！

`CONSTRAINT [外键名称] FOREIGN KEY(列1, 列2, ...) REFERENCES 父表名(父列1, 父列2, ...);`

父表中作为外键的列或者列组合必须建立索引，主键和具有唯一性约束的列默认的都建立了索引。

具有`AUTO_INCREMENT`属性的列必须建立索引

`MySQL`默认为主键字段加了`NOT NULL`属性，为其他字段加了`DEFAULT NULL`属性

查看表的结构

```
DESCRIBE 表名;
DESC 表名;
EXPLAIN 表名;
SHOW COLUMNS FROM 表名;
SHOW FIELDS FROM 表名;
SHOW CREATE TABLE 表名;
```

zerofill自动补0以及显示宽度

```
对于整数，如果在定义的时候指定了zerofill，当实际宽度不及显示宽度是时候，左侧会补上0.
每个整数类型都会有默认的显示宽度。TINYINT的默认显示宽度是4，INT的默认显示宽度是(11)。
可以指定，例如int(5)指定显示宽度为5，但是这个不影响他的存储范围
```

修改表

```
ALTER TABLE 旧表名 RENAME TO 新表名;
ALTER TABLE 表名 ADD COLUMN 列名 列的类型 [列的属性];
ALTER TABLE 表名 ADD COLUMN 列名 列的类型 [列的属性] FIRST;
ALTER TABLE 表名 ADD COLUMN 列名 列的类型 [列的属性] AFTER 指定列名;
ALTER TABLE DROP COLUMN 列名;
ALTER TABLE 表名 MODIFY 列名 新数据类型 [新属性];
ALTER TABLE 表名 MODIFY 列名 列的类型 列的属性 FIRST;
ALTER TABLE 表名 MODIFY 列名 列的类型 列的属性 after 指定列名;
ALTER TABLE 表名 CHANGE 旧列名 新列名 新数据类型 [新属性];   //这个可以用来对一个列进行改名字
```



### 类型转换

Mysql会根据环境自动做一些类型转换，例如  `'1'+'2'=3`。这里会把两个字符串当做数字1,2来处理，想要按照字符串来处理使用函数`CONCAT（’1‘，’2‘）`

### UNION

`UNION` 会自动去重 ，避免去重则使用`UNION ALL`

合并查询会把各个查询的结果汇总到一块，我们只能对最后总的结果集进行排序，而不能分别对各个查询进行排序。由于最后的结果集展示的列名是第一个查询中给定的列名，所以ORDER BY子句中指定的排序列也必须是第一个查询中给定的列名

### 插入

将搜索结果插入到表中

```SQL
INSERT INTO second_table(s, i) SELECT second_column, first_column FROM first_table WHERE first_column < 5;
```

```sql
INSERT IGNORE ....
在插入的时候，如果有错误会忽略掉，不会报错。
```

插入时遇到唯一性约束则进行更新

```SQL
INSERT INTO first_table (first_column, second_column) 
VALUES(1, '哇哈哈') 
ON DUPLICATE KEY UPDATE second_column = VALUES(second_column);
当遇到重复的key导致插入失败的时候改成更新数据
```

### 视图

```SQL
CREATE VIEW 视图名[(自定义的列名)] AS 查询语句
```

视图其实就是某个查询的**`别名`**，而不是某个查询的结果集，换句话说就是，创建视图的时候并不会把那个又臭又长的查询语句的结果集维护在硬盘或者内存里！在对视图进行查询时，MySQL服务器将会帮助我们把对视图的查询语句转换为对底层表的查询语句然后再执行

```SQl
SHOW CREATE VIEW 视图名;   #查看视图的定义
DROP VIEW 视图名; 			# 删除视图
```

`一般情况下，我们只在视图上执行查询操作而不进行更新操作，当然是可以进行更新的`



