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

