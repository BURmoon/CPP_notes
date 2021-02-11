## 1 select概述

> 整体形式：
>
> ​	[select col1, col2…]  [from table_name] [where condition] [group by col…] [having condtion] [order by col…]

- 语法格式

  `SELECT  * |{[DISTINCT] column | expression [alias],...}  FROM table;`

  - DISTINCT：重复记录只取一次
  - DISTINCT的作用范围不是距离它最近的列, 而是后面的所有的列

- 命令“/”：执行上一条成功执行的SQL语句

- 使用“c”(change)命令来修改上一条sql语句：`c  /错误关键字/正确关键字 `

  - 默认光标闪烁位置指向上一条SQL语句的第一行,输入2则定位到第二行
  - 使用“/”来执行修改过的SQL语句
  - 也可以使用ed(或者edit)命令来修改

- 使用别名 “as”

  案例：`select empno as "序号" from emp;`

  - 关键字as写与不写没有区别
  - 是否对别名使用双引号取决于别名中是否有空格，建议在用别名的时候加上""

- 算数运算 + - * /

  - 乘除的优先级高于加减
  - 优先级相同时, 按照从左至右运算
  - 可以使用括号改变优先级

- NULL值

  > 空值是无效的, 未指定的, 未知的或不可预知的值, 空值不是空格或者0

  - 包含NULL值的表达式都为空

  - NULL不等于NULL

    `select * from emp where NULL=NULL; `  error，查不到任何记录

    - 解决：滤空函数

      `nvl(a, b)`	--如果a为NULL，返回b

  -  判断一值是否等于NULL不用“=” 和“!=”而使用is和is not

    - 查询emp中comm为NULL的信息

      `select * from emp where comm is NULL;`

    - 查询emp中comm不为NULL的信息

      `select * from emp where comm is not NULL;`

- 连接符“||”

  > 在oracle中使用 || 连接字符串

  `select 'hello ' || 'world ' || '!!' from dual;`

  `select concat('hello ', 'world') from dual;`

  - concat函数只支持两个参数，不支持三个参数形式

  - dual	--伪表

    oracle中语法规定：select后面必须接from关键字

