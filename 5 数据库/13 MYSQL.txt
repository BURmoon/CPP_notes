## 1 数据库基本操作

> 数据库CURD：对数据库进行增(create)、删(delete)、改(update)、查(Retrieve)操作

- 创建数据库(默认为latin1)

  - 创建一个名称为mydb1的数据库

    `create database mydb1`

  - 创建一个使用utf-8字符集的mydb2数据库

    `create database mydb2 character set utf8`

  - 创建一个使用utf-8字符集，并带校对规则的mydb3数据库，会对存入的数据进行检查

    `create database mydb3 character set utf8 collate utf8_general_ci`

- 查看数据库

  - 显示所有数据库

    `show databases`

  - 显示创建数据库的语句信息

    `show create database mydb2`

- 修改数据库

  - 修改数据库mydb1的字符集为utf8(不能修改数据库名)

    `alter database mydb1 character set utf8`

- 删除数据库

  - 删除数据库mydb3

    `drop database mydb3`

## 2 表的操作

- 表的CURD

  `use mydb2`在mysql中对表操作前，必须先选择所使用的数据库

  `status`查看当前使用的是哪个库

  - 创建表

    `create table t1 (id int, name varchar(20))`

  - 查看表

    查看当前选择的数据库中的所有表：`show tables`

    查看表结构：`desc tablename`

    查看指定表的创建语句：`show create table employee`

  - 修改表

    更改表名：`rename table employee to worker`

    增加一个字段：`alter table employee add column height double`

    修改一个字段：`alter table employee modify column height float`

    删除一个字段：`alter table employee drop column height`

    修改表的字符集：`alter table employee character set gbk`

  - 删除表

    删除employee表：`drop table employee`

- 表数据的CURD

  - create数据

    ```
    create table employee(
    		id in，name varchar(20)，sex int,birthday date，
    		salary double，entry_date date，resume text
    )
    ```

  - Retrieve数据

    `select id, name as "名字", salary "月薪", salary*12 年薪  from employee where id >=2`

  - update数据

    `update employee set salary=salary+500`

  - delete数据 

    `delete from employee`

    使用truncate删除表中记录：`truncate employee`	无条件 效率高

