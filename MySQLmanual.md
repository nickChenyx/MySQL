# MySQL manual

> 在此处我非常感谢 **朱双印** 先生提供的帮助，他的[MySQL博客](http://www.zsythink.net/archives/tag/mysql/)写的十分清楚，在此篇文章结束之后可以去他的博客看看，在学习一次。

#### 登录 MySQL
```
mysql -h hostname  -u username -p
```
本机可以忽略 `-h` ，登录后输入密码即可，无密码直接回车进入。


#### 创建数据库
```
create database db_name [option];
```

可以通过 `show databases` 来查看已经创建好的数据库

#### 选择要操作的数据库
已经登录了可以使用：
```
use db_name;
```
还未登录 MySQL 的可以：
```
mysql -D db_name -h hostname -u username -p
```
直接登录即选中要操作的数据库。

#### 创建数据库表
```
create table table_name(statement);
```
例如：
```
create table user(
    id int not null primary key,
    name char(8) not null
);
```

对于一些较长的语句，在 cmd 终端的命令提示符(`mysql>`)下容易输错，可以直接在文本编辑器内输入好后保存为 `user.sql` 文件，通过执行脚本完成数据表的创建。
```
mysql -D db_name -u username -p < yoursql.sql
```

- 可以使用 `show tables;` 可以查看已经创建的表名 
- 可以使用 `describe table_name;` 可以查看已创建的表的详细信息

#### 向表中插入数据
```
insert into table_name [(col1,col2,col3,...)] values (v1,v2,v3,...);
```
其中 `[]` 中的内容是可选的，如果插入全部数据的话可以不用写，但是如果只插入部分数据的话，需要指明要插入的列名称。

#### 查询表中的数据
```
select column_name from table_name [option]；
```
可以使用通配符 "*" 匹配所有的列，`select * from user`;

**按特定条件查询**  
`where` 关键字用于指定查询条件。where 子句不仅仅支持 "where column_name = value" 这种名等于值的查询形式, 对一般的比较运算的运算符都是支持的, 例如 =、>、<、>=、<、!= 以及一些扩展运算符 is [not] null、in、like 等等。 还可以对查询条件使用 or 和 and 进行组合查询。

#### 更新表中的数据
```
update table_name set colum_name=value [where condition];
```
- 将id为5的手机号改为默认的"-": `update students set tel=default where id=5;`
- 将所有人的年龄增加1: `update students set age=age+1;`
- 将手机号为 13288097888 的姓名改为 "张伟鹏", 年龄改为 19: `update students set name="张伟鹏", age=19 where tel="13288097888";`


#### 删除表中的数据
```
delete from table_name [where condition];
```
- 删除id为2的行: `delete from students where id=2;`
- 删除所有年龄小于21岁的数据:`delete from students where age<20;`
- 删除表中的所有数据: `delete from students;`

#### 创建表后的修改
**添加列：**
```
alter table table_name add column_name column_type [after position];
```
- 在表的最后追加列 address: `alter table students add address char(60);`
- 在名为 age 的列后插入列 birthday: `alter table students add birthday date after age;`  

**修改列：**
```
alter table table_name change column_name new_column_name new_column_type;
```
- 将表 tel 列改名为 telphone: `alter table students change tel telphone char(13) default "-";`
- 将 name 列的数据类型改为 char(16): `alter table students change name name char(16) not null;`

**删除列：**
```
alter table table_name drop column_name;
```

**重命名表：**
```
alter table table_name rename new_table_name;
```

**删除整张表：**
```
drop table table_name;
```

**删除整个数据库：**
```
drop database db_name;
```


#### 约束、索引、键

**非空约束：**
添加：
```
ALTER TABLE table_name MODIFY column_name not null;
```
删除：
```
ALTER TABLE table_name MODIFY column_name null;
```

**自动增长：**
添加：
```
ALTER TABLE table_name MODIFY column_name int auto_increment;
ALTER TABLE table_name CHANGE column_name column_name int auto_increment;
```
删除
```
ALTER TABLE table_name CHANGE column_name column_name int;
ALTER TABLE table_name MODIFY column_name int;
```

**主键约束：**
添加：
```
ALTER TABLE table_name add primary key(id);
ALTER TABLE table_name add constraint primary key(id);
```
删除：
```
ALTER TABLE table_name DROP primary key;
```

**唯一键约束：**
添加：
```
ALTER TABLE table_name add unique key(uid);
# 给唯一键指定名称
ALTER TABLE table_name add unique key uni_id(uid);
```
删除：
```
ALTER TABLE table_name DROP index uni_id;
```

**查看约束：**
如下语句可以显示出表中的 **主键信息、唯一键信息、外键信息** ，(不包括纯索引index信息)
```
select * from information_schema.key_column_usage where table_name='table_name';
```
也可以直接查看表结构:
```
DESC table_name
```

**查看主外键对应关系**
查看指定表中引用了哪些表中的主键作为自己的外键，可以理解为查看指定表的外键：
```
SELECT TABLE_NAME,COLUMN_NAME,CONSTRAINT_NAME,REFERENCED_TABLE_NAME,REFERENCED_COLUMN_NAME FROM information_schema.KEY_COLUMN_USAGE WHERE TABLE_NAME='table_name' AND REFERENCED_TABLE_NAME IS NOT NULL;
```

查看指定表中的主键被哪些表引用成为了外键：
```
SELECT REFERENCED_TABLE_NAME,REFERENCED_COLUMN_NAME,CONSTRAINT_NAME,TABLE_NAME,COLUMN_NAME FROM information_schema.KEY_COLUMN_USAGE WHERE REFERENCED_TABLE_NAME ='table_name';
```

**外键约束：**
添加：  
这里的 tname 是指定的外键约束名  
```
ALTER TABLE table_name ADD COLUMN tid int default 0 not null;
ALTER TABLE table_name ADD CONSTRAINT tname FOREIGN KEY(tid) REFERENCES another_table_name(id);
```
删除：
```
ALTER TABLE table_name DROP FOREIGN KEY tname;
```

**索引管理：**
添加一个索引/创建一个纯粹的索引（指的就是index，而不是unique index）
```
# ind_name 是索引名 name 是字段名
ALTER TABLE table_name ADD INDEX ind_name(name);
```

另一种方式：  
默认升序排列，20是指取出name左边开头的20个字节作为索引
```
create index ind_name on testtb (name(20) desc);
create index ind_name on testtb (name(20));
create index ind_name on testtb (name);
```

创建联合索引：
```
create index ind_id_name on testtb1 (id,name);
create index ind_id_name on testtb1 (id,name(20));
```

重建索引：
```
repair table table_name quick;
```

删除索引：
```
ALTER TABLE table_name DROP INDEX ind_name;
```

查看索引：
```
show index from table_name;
```




### SELECT 

#### group by 分组
```
SELECT * FROM table_name GROUP BY column_name;
```

#### 聚合函数

**min(col):**返回指定列的最小值
**max(.):**
**avg(.):**返回指定列均值
**count(.):**返回指定列中的非null的个数
**sum(.):**返回指定列所有值之和
**group_concat(.):**返回指定列的值，分组显示

#### having
```
SELECT * FROM table_name GROUP BY colum_name HAVING id > 1;
```

[ref 多表查询](http://www.zsythink.net/archives/1105)
#### 交叉连接 cross join
同时查找两张表，表中的数据会作为笛卡尔积的形式展示。例如表A有abc三条数据，表B有12两条数据。查询出来的数据为{a1,a2,b1,b2,c1,c2}
```
SELECT * FROM t1,t2;
# 实际等同于 
SELECT * FROM t1 cross join t2;
```

#### 内连接 inner join 等效于 join
两张表中同时符合某种条件的数据记录的集合。例如两张表中id相同的数据集合。
```
SELECT * FROM t1,t2 WHERE t1.id=t2.id;
# 更好的写法
SELECT * FROM t1 inner join t2 on t1.id=t2.id;
```

#### 外连接： left join, right join
直接看[引用](http://www.zsythink.net/archives/1105)

#### 联合查询：union  union all
同上

#### 全连接：full join
同上


### 查询缓存
[引用](http://www.zsythink.net/archives/1111)


