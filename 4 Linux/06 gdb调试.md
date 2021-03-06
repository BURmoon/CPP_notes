## 1 gdb设置

- 生成调试信息

  - 使用编译器（cc/gcc/g++）的 -g 参数，把调试信息加到可执行文件中

    `gcc -g hello.c -o hello`

- 启动gdb

  - `gdb program`	--program 是执行文件

- 设置运行参数

  - `set args xxx`	 --指定运行时参数
  - `show args`		--查看设置好的运行参数

- 启动程序

  - run	--程序开始执行, 如果有断点, 停在第一个断点处
  - start	--程序向下执行一行(在第一条语句处停止)

- 显示源代码

  - list	--显示当前行后面的源程序
  - list -	--显示当前文件开始处的源程序
  - list function	--显示函数名为function的函数的源程序
  - list linenum	--打印第linenum行的上下文内容
  - list file:function	--显示file文件的函数名为function的函数的源程序
  - list file:linenum	--显示file文件下第linenum行

- 设置显示的范围

  > 用list命令来打印程序的源代码, 默认打印10行

  - set listsize n	--设置一次显示源代码的行数
  - show listsize	--查看当前listsize的设置

- 设置断点

  - b linenum		--设置断点，在源程序第linenum行
  - b func		--设置断点，在func函数入口处
  - b file:linenum	--设置断点，在源文件filename的linenum行处停住
  - b file:func	 	--设置断点，在源文件filename的function函数的入口处停住

- 查询所有断点

  - info b == info break == i break == i b

- 条件断点

  - 使用if关键词, 后面跟其断点条件

    `b test.c:8 if intValue == 5`

- 维护断点

  - disable m n | m-n	--使指定断点无效
  - enable m n | m-n	--使无效断点生效
  - delete m n | m-n	--删除指定的断点

## 2 gdb调试

- 调试命令
  - run	--运行程序，可简写为 r
  - start	--程序向下执行一行
  - next	--单步跟踪，函数调用当作一条简单语句执行，可简写为n
  - step	--单步跟踪，函数调进入被调用函数体内，可简写为s
  - finish	--退出进入的函数
  - until	--在一个循环体内单步跟踪时，这个命令可以运行程序直到退出循环体，可简写为u
  - continue	--继续运行程序，可简写为c(若有断点则跳到下一个断点处)
- 查看变量的值
  - print var	--打印变量、字符串、表达式等的值，可简写为p
- 查看修改变量的值
  - p type width	--查看变量width的类型
  - p width 		--打印变量width的值
  - set var width=47	--将变量var值设置为47
- 自动显示变量
  - display var	--程序停住时，自动显示var变量
  - info display	--查看display设置的自动显示的信息
  - disable display m n | m-n	--使自动显示无效
  - enable display m n | m-n	--使自动显示有效
  - delete display m n | m-n	--删除自动显示
  - undisplay m n | m-n	--删除自动显示
