## 1 常用函数

> 查找函数库库的路径：`find / -name libmysqlclient*`

- 初始化：`MYSQL *mysql_init(MYSQL *mysql)`

- 错误处理

  `unsigned int mysql_errno(MYSQL *mysql)`

  `char *mysql_error(MYSQL *mysql)`

- 建立连接

  ```
  MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user, 
  				const char *passwd, const char *db, unsigned int port, 
  				const char *unix_socket, unsigned long client_flag)
  ```

  - 案例：`mysql = mysql_real_connect(mysql, "localhost", "root", "123456", "mydb61", 0, NULL, 0);`

- 执行SQL语句：`int mysql_query(MYSQL *mysql, const char *stmt_str)`

  - 案例

    `char *psql = "select * from emp";`

    `ret = mysql_query(mysql, psql);`

- 获取结果

  将整个结果集全部取回来：`MYSQL_RES *mysql_store_result(MYSQL *mysql)`

  成功返回MYSQL_RES结果集指针，失败返回NULL

- 解析结果集：`MYSQL_ROW mysql_fetch_row(MYSQL_RES *result)`

  成功返回下一行的MYSQL_ROW结构

  如果没有更多要检索的行或出现了错误，返回NULL

- 获取列数

  从mysql句柄中获取有多少列：`unsigned int mysql_field_count(MYSQL *mysql)`

  从返回的结果集中获取有多少列：`unsigned int mysql_num_fields(MYSQL_RES *result) `

  - 案例

    ```
    int num = mysql_field_count(connect);
    while (row = mysql_fetch_row(result)) {
    	for (i = 0; i < num; i++) {
    	printf("%s\t", row[i]);
    	}
    }
    ```

- 获取表头

  全部获取：`MYSQL_FIELD *mysql_fetch_fields(MYSQL_RES *result)`

  获取单个：`MYSQL_FIELD *mysql_fetch_field(MYSQL_RES *result)`

- 释放内存：`void mysql_free_result(MYSQL_RES *result)`

- 关闭连接：`void mysql_close(MYSQL *mysql)`



