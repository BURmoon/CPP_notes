## 1 视图

> 视图本身没有数据, 数据存储在表中

- 使用视图可以限制用户对某些数据的访问，可以简化查询

- 不能通过视图去修改表的数据

  可以将视图设置为只读属性:with read only

  `create or replace view vm_emp as select * from emp with read only`

- 创建视图

  ```
  create view vm_emp as select * from emp;
  create or replace view vm_emp as select * from emp;
  create or replace view vm_emp as select deptno, empno, ename from emp where deptno=10;
  select view_name from user_views;
  ```

- 删除视图

  `drop view vm_emp`

## 2 索引

> 使用是索引的目的是提高查询的效率

- 索引的原理

  - 在查询的时候，where条件后面要使用创建索引的时候的列，oracle先查询索引表
  - 从索引表中找到该列的值对应的rowid，找到rowid再从表中根据rowid找到那一行记录

- 创建：`create index idx_emp on emp(empno)`

- 删除：`drop index idx_emp_bak`

- 查询创建的索引

  `select index_name from user_indexes`

  `select xxxx_name from user_xxxxs`

## 3 序列

> 由于表的主键要求是非空且唯一的，为了保证主键是非空和唯一的，可以使用序列

- 创建序列：` create sequence seq_mytest`
- 删除序列：`drop sequence seq_mytest`
- 序列的属性
  - currval 和 nextval，但是第一次使用的时候先要取nextval的值

## 4 同义词

> 同义词就是别名

- 同义词使用的场合

  - 用户1想访问scott用户的emp表，需要scott用户给用户1赋访问emp表的权限

    `grant select on emp to 用户1`

  - 然后使用用户1用户登录oracle

    `sqlplus 用户1/用户1@oracle`

    `select * from scott.emp`

    为了在访问scott.emp表的时候不用再使用scott.emp，可以给scott.emp创建同义词

- 创建同义词：` create synonym emp for scott.emp`
- 删除同义词：`drop synonym emp`

```
创建一个新的oracle用户:
	//使用sys用户创建新的用户和给这个新用户添加权限
	create user New identified by New;
	grant connect, resource to New;
	grant create synonym to New;
```

