## 1 概述

- 体系结构
  - Oracle服务器：

    由1个数据库和一个(或多个)实例组成（数据库位于硬盘上，实例位于内存中）

  - 表空间users：

    表空间由多个数据文件组成（位于实例上，在内存中），数据文件位于硬盘之上

  - 段：

    段存在于表空间中，段是区的集合

  - 区：

    区是数据块的集合

  - 数据块：

    数据块会被映射到磁盘块

- SQL和sqlplus

  - SQL是语言，关键字不能缩写

  - sqlplus是oracle提供的工具(是一种环境)，可在里面执行SQL语句，配有自己的命令(ed、c、set、col) 

    特点是缩写关键字

- 在SQL中，判断一值是否等于NULL不用“=” 和“!=”而使用is和is not

- 在SQL中，双引号“”表示别名，使用‘’来表示字符串

- 在Oracle中，定义一张“伪表”dual用来满足语法规定：select后必须接from

- where条件过滤中，对字符串大小写敏感

- 常见的数据库对象

  - 表		基本的数据存储集合，由行和列组成
  - 视图		从表中抽出的逻辑上相关的数据集合
  - 序列		提供有规律的数值
  - 索引		提高查询的效率
  - 同义词		给对象起别名

## 2  启动oracle服务和连接oracle数据库

- windows下的oracle的启动

  - 启动OracleServiceORCL		--oracle数据库服务系统
  - 启动home1TNSListener		--监听服务，用于远程连接的监听

- linux下启动oracle数据库

  - 使用linux的oracle用户登陆：`sqlplus sys/sys as sysdba`

    SQL> startup   		--启动数据库服务

    SQL> exit   				--退出数据库服务

  - 启动监听服务：`lsnrctl start`

  - linux下停止oracle数据库

    SQL> shutdown immeidate		--关闭数据库服务

- 使用sqlplus登陆oracle数据库

  - 普通用户登陆
    - `[cmd]sqlplus scott/tiger@192.168.10.145/orcl`
    - 或 `sqlplus 用户名/密码`
  - 管理员登陆
    - `[cmd]sqlplus scott/tiger@oracle as sysdba`
    - 或 `sqlplus sys/sys as sysdba`
  - 解锁用户：`alter user scott account unlock;`
  - 加锁用户：`alter user scott account lock;`
  - 修改用户密码：`alter user scott identified by xxxxx;`

## 3 基本操作

- 显示当前用户：`show user;`

- 查看当前用户下的表：`select * from tab;`

  > tab	数据字典（记录数据库和应用程序源数据的目录），包含当前用户下的表

- 查看员工表的结构：`desc emp;`

- 设置行宽：`set linesize 120;`

- 设置页面：`set pagesize 100;`

  > 更改配置文件，永久设置行宽与页面（将上述两行写入如下两个配置文件）
  >
  > C:\app\Administrator\product\11.2.0\client_1\sqlplus\admin\glogin.sql
  >
  > C:\app\Administrator\product\11.2.0\dbhome_1\sqlplus\admin\glogin.sql

- 设置员工名列宽：`col ename for a20` (a表示字符串)

- 设置薪水列为4位数字：`col sal for 9999 `(一个9表示一位数字)

- 日期格式

  - 获取当前系统的日期：`select sysdate from dual;`

  - 获取当前系统的日期格式：`select * from v$nls_parameters;`

  - 修改日期格式

    ​	`alter session set NLS_DATE_FORMAT = 'yyyy-mm-dd ';`

    ​	`alter session set NLS_DATE_FORMAT = 'yyyy-mm-dd hh24:mi:ss';`

  - 将日期格式改回默认设置

    ​	`alter session set NLS_DATE_FORMAT = 'DD-MON-RR';`

