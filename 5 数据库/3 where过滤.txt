## 1 where条件过滤

> 在where条件中使用的列的值对大小写是敏感的, 如是字符串需要用单引号引起来

- 案例：查询 emp 中 hiredate 为1981年11月17日的信息

  `select * from emp where hiredate = '17-11月-81 ';`

## 2 比较运算

- 普通比较运算符

- between…and：介于两值之间,闭区间,包含两边的值

- in	--在集合中	

  not in 	--不在集合中

  - NULL空值：如果结果中含有NULL，不能使用 not in 操作符，但可以使用 in 操作符

    `not in (10, 20, NULL)`相当于:

    `deptno!=10 and deptno!=20 and deptno!=NULL`	而包含NULL的表达式都为空

- like模糊查询

  - `%`匹配任意多个字符，`_`匹配一个字符，使用`escape`表示转义字符

    案例：查询emp中ename包含_的信息

    `select * from emp where ename like '%\_% ' escape '\';`

## 3 逻辑运算

- AND	 	逻辑并

  OR 		逻辑或

  NOT	 	逻辑非

- SQL在解析where的时候，是从右至左解析的，所以

  and时应该将易假的值放在右侧， or时应该将易真的值放在右侧

## 4 order by 排序

> ORDER BY子句在SELECT语句的最末尾, 是对select查询的最后的结果进行排序

- 使用 `ORDER BY` 子句排序

  - ASC(ascend)	--升序，默认采用升序方式
  - DESC(descend)	--降序

- 语法：`order by + 列名/序号/表达式/别名`

  案例： `select  ename, sal, sal*12  from emp order by sal * 12 desc`

  ​			默认ename→1， sal→2，sal*12→3，上面一条语句又可写成

  ​			`select  ename, sal, sal*12, from emp order by 3 desc`

- 按照两列或者多列进行排序

  - 规则：先按照第一列排序，如果相同则按照第二列排序，以此类推
  - order by后有多列时，列名之间用逗号隔分，order by会同时作用于多列
  - desc只作用于最近的一列, 两列都要降序排, 则需要两个desc

- 排序时将空值放在最后

  `select * from emp order by comm desc nulls last`

