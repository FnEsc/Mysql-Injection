# 八、Background-4 增删改函数介绍

在对数据进行处理上，我们常用的是增删查改。查就是上述总用到的select，接下来讲解mysql的增删改。

### 增加 Insert

官方文档 help insert 得到：
```sql
INSERT [LOW_PRIORITY | DELAYED | HIGH_PRIORITY] [IGNORE]
    [INTO] tbl_name
    [PARTITION (partition_name,...)]
    [(col_name,...)]
    {VALUES | VALUE} ({expr | DEFAULT},...),(...),...
    [ ON DUPLICATE KEY UPDATE
      col_name=expr
        [, col_name=expr] ... ]
...
```

> 简单举例：<br>
`insert into users values('16','1camry','1camry')`

### 删除 delete drop 

- 删除数据：<br>
detele from 表名;<br>
delete from 表名 where id=1;
- 删除结构：<br>
删数据库：drop database 数据库名;<br>
删除表：drop 表名;<br>
删除表中的列：alter table 表名 drop column 列名;<br>

> 简单举例：<br>
delete from users where id=16;


### 修改

- 修改所有：update 表名 set 列名='新的值，非数值加单引号';
- 带条件的修改：update 表名 set 列名='新的值，非数字加单引号' where id=6;


