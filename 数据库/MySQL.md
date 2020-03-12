# MySQL
## 一、安装
### 1.1 下载
### 1.2 使用
> dos命令打开MySQL
```sql
-- 启动mysql服务,net start 服务名(我的是mysql5.7)
-- 在mysql安装路径的bin\下
-- \bin> mysql -h 主机名(本地可省略-h) -u 用户名 -p 密码(无,不写)
```

## demo
> 学生表: student

| 字段     | 类型               | 说明                  |
| -------- | ------------------ | --------------------- |
| id       | bigint(5),not null | 主键,自增             |
| stu_name | varchar(5),not null         | 姓名                  |
| age      | int(5)             | 年龄                  |
| gender   | int(1)             | 性别,1:男,0:女        |
| birth    | datetime           | 出生年月,'yyyy-MM-dd' |

### 建库,建表
```sql
-- 1.建库tsetdb
create database if not exists testdb default charset utf8;
-- 1.1 删库
drop database testdb;
-- 2.切换到当前数据库 testdb
use testdb;
-- 3.建表student
create table if not exists student(
	id bigint(5) not null primary key auto_increment,
	stu_name varchar(5) not null,
    age int(5), 
    gender int(1),
    birth datetime
);
-- 4.新增/插入数据
insert into student(id,stu_name,age,gender,birth) values(null,'诸葛亮',18,1,'2020-03-12');

insert into student values(0,'刘备',20,1,"2020-03-12"),(0,'曹操',18,1,"2020-03-11");

-- 5.删除表内数据
delete from student where id = 1;
-- 5.1删除表内数据,保存数据结构
truncate table student;
-- 5.2删除表内数据和表结构
drop table student;

-- 修改数据
update student set  stu_name='baoabo',age=12 where id=1;
-- 高级查询
```

## 索引(index)
+ 主键索引
+ 唯一索引unique
  + 唯一索引列的值必须唯一,但允许为空值 
+ 普通索引 index
  + 最基本的索引
+ 组合索引
  + 一个索引包含多个列
  + index_group(id,name,age)
  + 索引顺序: id,(id,name),(id,name,age)
+ 全文索引 fulltext

## 连接 join
+ 