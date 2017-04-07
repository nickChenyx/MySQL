# 快速上手 MySQL

>这篇文章会提供需要进行上手的全部文件！也作为我自己学习的一个资料。在此处我非常感谢 **朱双印** 先生提供的帮助，他的[MySQL博客](http://www.zsythink.net/archives/tag/mysql/)写的十分清楚，在此篇文章结束之后可以去他的博客看看，在学习一次。


## 第一步，进门

#### 登录 MySQL
```
mysql -h hostname  -u username -p
```
本机可以忽略 `-h` ，登录后输入密码即可，无密码直接回车进入。


#### 创建数据库
创建一个测试数据库供使用：
```
create database test;
```

#### 选择数据库
如果是刚进 MySQL 的话，需要选择数据库。
```
use test;
```

#### 创建数据库
创建一个学生表供使用：
```
CREATE TABLE stu(
    id INT UNIQUE NOT NULL PRIMARY KEY,
    s_name VARCHAR(20) NOT NULL,
    s_grade VARCHAR(20)
) ENGINE=INNODB CHARSET=utf8;

```

## 第二步，麻瓜操作

#### 向表中插入数据
两种插入方式，全部数据和部分数据：
```
INSERT INTO stu VALUES(1,'王智','大一');
INSERT INTO stu(id,s_name) VALUES('2','小明');
```

#### 查询表中的数据
```
SELECT * FROM stu;
SELECT id,s_name FROM stu;
SELECT * FROM stu WHERE id=1;
```
结果分别是 
```
    id  s_name  s_grade    
------  ------  ---------
     1  王智      大一   
     2  小明      (NULL)   
///////////////////////////////
    id  s_name  
------  --------
     1  王智  
     2  小明  
///////////////////////////////
    id  s_name  s_grade  
------  ------  ---------
     1  王智      大一   
```


#### 更新表中的数据
```
UPDATE stu SET s_grade='小学一年级' WHERE id=2;
```
修改完之后，数据是这样子的：
```
    id  s_name  s_grade          
------  ------  -----------------
     1  王智      大一           
     2  小明      小学一年级  
```


## 第三步，小试牛刀

#### 创建表后的修改

**添加列：**
```
ALTER TABLE stu ADD age INT AFTER s_name;
```
在 `s_name` 之后添加一行 `age` 用来记录年龄，修改之后是这样的：
```
    id  s_name     age  s_grade          
------  ------  ------  -----------------
     1  王智      (NULL)  大一           
     2  小明      (NULL)  小学一年级  
```


**修改列：**
之前的 `age` 字段格式和别的字段不同，修改成 `s_age` 格式一致：
```
ALTER TABLE stu CHANGE age s_age INT(3);
```
效果如下：
```
    id  s_name   s_age  s_grade          
------  ------  ------  -----------------
     1  王智      (NULL)  大一           
     2  小明      (NULL)  小学一年级  
```


**删除列：**
不想要 `s_age` 字段了
```
ALTER TABLE stu DROP s_age;
```
结果如下：
```
    id  s_name  s_grade          
------  ------  -----------------
     1  王智      大一           
     2  小明      小学一年级  
```


**重命名表：**
```
ALTER TABLE stu RENAME student;
```
此时的表名为 `student`

**删除整张表：**
```
DROP TABLE student;
```
此时 `student` 表在数据库中消失了


**删除整个数据库：**
```
DROP DATABASE test;
```
此时 `test` 数据库已被删除


## 第四步 NoMagic

弄个新的表：
```
CREATE TABLE `test`.`person`(  
  `id` INT UNSIGNED NOT NULL,
  `name` VARCHAR(20),
  `sex` CHAR,
  `age` INT,
  PRIMARY KEY (`id`)
) ENGINE=INNODB CHARSET=utf8;
```

再弄点数据来用：
```
INSERT INTO person VALUES(1 ,'person1','m',60);
INSERT INTO person VALUES(2 ,'person2','m',78);
INSERT INTO person VALUES(3 ,'person3','m',58);
INSERT INTO person VALUES(4 ,'person4','m',58);
INSERT INTO person VALUES(5 ,'person5','f',56);
INSERT INTO person VALUES(6 ,'person6','m',47);
INSERT INTO person VALUES(7 ,'person7','f',58);
INSERT INTO person VALUES(8 ,'person8','m',15);
INSERT INTO person VALUES(9 ,'person9','m',78);
INSERT INTO person VALUES(10 ,'person10','m',74);
INSERT INTO person VALUES(11 ,'person11','m',68);
INSERT INTO person VALUES(12 ,'person12','f',55);
INSERT INTO person VALUES(13 ,'person13','f',48);
INSERT INTO person VALUES(14 ,'person14','f',29);
INSERT INTO person VALUES(15 ,'person15','m',66);
INSERT INTO person VALUES(16 ,'person16','m',69);
INSERT INTO person VALUES(17 ,'person17','m',32);
INSERT INTO person VALUES(18 ,'person18','m',23);
INSERT INTO person VALUES(19 ,'person19','m',18);
```

此时的表格数据为：
```
    id  name      sex        age  
------  --------  ------  --------
     1  person1   m             60
     2  person2   m             78
     3  person3   m             58
     4  person4   m             58
     5  person5   f             56
     6  person6   m             47
     7  person7   f             58
     8  person8   m             15
     9  person9   m             78
    10  person10  m             74
    11  person11  m             68
    12  person12  f             55
    13  person13  f             48
    14  person14  f             29
    15  person15  m             66
    16  person16  m             69
    17  person17  m             32
    18  person18  m             23
    19  person19  m             18
```



#### group by 分组
按照性别分组一下：
```
SELECT * FROM person GROUP BY sex;
```
结果：
```
    id  name     sex        age  
------  -------  ------  --------
     5  person5  f             56
     1  person1  m             60
```
这里按性别分组，每组都只显示了第一个数据，后面会讲到如何显示全部数据。


#### 聚合函数

**min(col):**返回指定列的最小值  
**max(.):**  
**avg(.):**返回指定列均值  
**count(.):**返回指定列中的非null的个数  
**sum(.):**返回指定列所有值之和  
**group_concat(.):**返回指定列的值，分组显示  


直接查询一下男女各有多少人：
```
SELECT COUNT(id) AS num,sex FROM person GROUP BY sex;
```
结果：
```
   num  sex     
------  --------
     5  f       
    14  m       
```

这里的 **group_concat(.)**可以将指定值返回，上面的名字就可以利用这个函数全部显示出来：
```
SELECT id,sex,GROUP_CONCAT(NAME) FROM person GROUP BY sex;
```
结果：
```
    id  sex     group_concat(name)                                                                                                      
------  ------  ------------------------------------------------------------------------------------------------------------------------
    12  f       person12,person13,person14,person7,person5                                                                              
    15  m       person15,person16,person17,person18,person1,person11,person10,person9,person8,person6,person4,person3,person2,person19  
```


#### having
查询一下男女是否平均年龄大于50：
```
SELECT sex,AVG(age) AS age FROM person GROUP BY sex  HAVING age>50;
```
结果如下：
```
sex     age      
------  ---------
m       53.1429  
```
男性平均年龄53岁多。
