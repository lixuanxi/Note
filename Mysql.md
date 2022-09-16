**mysql 启动**

`net start [mysql](注册名称)`

`net stop [mysql]`

**客户端连接**

`mysql [-h 127.0.0.1] [-p 3306] -u root -p`



### 2.3.1 数据库操作

**1). 查询所有数据库**

`show databases `

**2). 查询当前数据库**

`select database() ;`

**3). 创建数据库**

`create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序 规则 ] ;`

A. 创建一个itcast数据库, 使用数据库默认的字符集

`create database itcast;`

B. 创建一个itheima数据库，并且指定字符集

`create database itheima default charset utf8mb4;`

**4). 删除数据库**

`drop database [ if exists ] 数据库名 ;`

**5). 切换数据库**

`use 数据库名 ;`



### 2.3.2 表操作

#### **2.3.2.1 表操作-查询创建**

**1). 查询当前数据库所有表**

`show tables;`

**2). 查看指定表结构**

`desc 表名 ;`

**3). 查询指定表的建表语句**

`show create table 表名 ;`

**4). 显示表列**

`show columns from [table] ;`

**5). 创建表结构**

```sql
REATE TABLE 表名(
	字段1 字段1类型 [COMMENT 字段1注释 ],
	字段2 字段2类型 [COMMENT 字段2注释 ],
	字段3 字段3类型 [COMMENT 字段3注释 ],
	......
	字段n 字段n类型 [COMMENT 字段n注释 ]
) [ COMMENT 表注释 ] ;
```





## 检索数据

**SELECT语句：它的用途是从一个或多个表中检索信息。**

**检索单个列**

`select [columns] from [tables] `

**检索多个列**

`select [columns1, columns2, ...] from [tables] `

**检索所有列**

`select * from [tables]`：*（通配符）

**检索不同的行**

`select distinct [columns] from [tables]`：关键词 `distinct` 去重

**限制结果**

`select [columns] from [tables] limit n `：限制结果不多于n行

**使用完全限定的表名**

`select [tables.columns] from [databases.tables] `



## 排序检索数据

**子句**：SQL语句由子句构成，有些子句是必需的，而 有的是可选的。

**为了明确地排序用SELECT语句检索出的数据，可使用ORDER BY子句。 **

**ORDER BY子句取一个或多个列的名字，据此对输出进行排序。**

**排序数据**

`select [columns] from [tables] order by [columns]`:ORDER BY子句对输出排序

**按多个列排序**

`select [columns1, columns2, ...] from [tables] order by [columns3, columns4]`：检索1，2按3，4排序

**指定排序方向**

`select [columns1, columns2, ...] from [tables] order by [columns] DESC`：DESC降序



## 过滤数据

**在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤。**

**WHERE子句在表名（FROM子句）之后给出**

**使用WHERE子句**

`select [columns] from [tables] where [columns = xx]`：返回满足where条件的行

**WHERE子句操作符**

MySQL在执行匹配时默认不区分大小写

| 操作符  |        说明        |
| :-----: | :----------------: |
|    =    |        等于        |
|   <>    |       不匹配       |
|   !=    |       不等于       |
|    <    |        小于        |
|   <=    |      小于等于      |
|    >    |        大于        |
|   \>=   |      大于等于      |
| BETWEEN | 在指定的两个值之间 |



## 数据过滤

**为了进行更强的过滤控制，MySQL允许给出多个WHERE子句。这些子 句可以两种方式使用：以AND子句的方式或OR子句的方式使用。**

**操作符（operator） 用来联结或改变WHERE子句中的子句的关键 字。也称为逻辑操作符（logical operator）。**

**AND操作符**

`AND` 用在WHERE子句中的关键字，用来指示检索满足所有给定 条件的行。

**OR操作符**

`OR` 操作符与 `AND` 操作符不同，它指示MySQL检索匹配任一条件的行。

**计算次序**

`WHERE` 可包含任意数目的 `AND` 和 `OR` 操作符。允许两者结合以进行复杂和高级的过滤。

`AND` 在计算次序中优先级更高，使用圆括号可以明确地分组相应的操作符。

**IN操作符**

`IN` 操作符用来指定条件范围，范围中的每个条件都可以进行匹配。`IN` 取合法值的由逗号分隔的清 单，全都括在圆括号中。

`IN` WHERE子句中用来指定要匹配值的清单的关键字，功能与 `OR` 相当。

**NOT操作符**

`NOT` WHERE子句中用来否定后跟条件的关键字。



## 用通配符进行过滤

**LIKE操作符**

通配符（wildcard） 用来匹配值的一部分的特殊字符

搜索模式（search pattern）由字面值、通配符或两者组合构 成的搜索条件。

为在搜索子句中使用通配符，必须使用LIKE操作符。

**LIKE指示MySQL， 后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。**

**百分号（%）通配符**

在搜索串中，`%` 表示任何字符出现任意次数。

通配符可在搜索模式中任意位置使用，并且可以使用多个通配符。

根据MySQL的配置方式，搜索可以是区分大小写的。

虽然似乎 `%` 通配符可以匹配任何东西，但有一个例外，即 `NULL`。即使是`WHERE prod_name LIKE '%'`也不能匹配用值NULL作为产品名的行。

**下划线（_）通配符**

下划线的用途与 `%` 一样，但下划线只匹配单个字符而不是多个字符。

与`%`能匹配0个字符不一样，`_`总是匹配一个字符，不能多也不能少。



**使用通配符的技巧**

- 不要过度使用通配符。如果其他操作符能达到相同的目的，应该 使用其他操作符。
- 在确实需要使用通配符时，除非绝对有必要，否则不要把它们用 在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。
- 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。



## 用正则表达式进行搜索

正则表达式是用来匹配文本的特殊的串（字符集合）。

**基本字符匹配**

`REGEXP` 后所跟的东西作 为正则表达式处理

`.` 是正则表达式语言中一个特殊的字符。它表示匹配任意一个字符

LIKE与REGEXP:

LIKE匹配整个列。如果被匹配的文本在列值中出现，LIKE将不会找到它，相应的行也不被返回（除非使用通配符）。而REGEXP在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP将会找到它，相应的行将被返回。

**进行OR匹配**

为搜索两个串之一（或者为这个串，或者为另一个串），使用`|`

使用 `|` 从功能上类似于在SELECT语句中使用OR语句，多个 `OR` 条件可并入单个正则表达式。

**匹配几个字符之一**

可通过指定一组用`[ ]` 括起来的字符来完成匹配任何单一字符

**匹配范围**

集合可用来定义要匹配的一个或多个字符。

可使用 `-` 来定义一个范围

范围不限于完整的集合，`[1-3]` 和 `[6-9]` 也是合法的范围。此外，范围不一定只是数值的，`[a-z]`匹配任意字母字符。

**匹配特殊字符**

为了匹配特殊字符，必须用 `\\`为前导。`\\-` 表示查找 `-` ，`\\.` 表示查找 `.`

**匹配字符类**

|     类     |                       说 明                       |
| :--------: | :-----------------------------------------------: |
| [:alnum:]  |          任意字母和数字（同[a-zA-Z0-9]）          |
| [:alpha:]  |              任意字符（同[a-zA-Z]）               |
| [:blank:]  |               空格和制表（同[\\t]）               |
| [:cntrl:]  |         ASCII控制字符（ASCII 0到31和127）         |
| [:digit:]  |                任意数字（同[0-9]）                |
| [:graph:]  |           与[:print:]相同，但不包括空格           |
| [:lower:]  |              任意小写字母（同[a-z]）              |
| [:print:]  |                  任意可打印字符                   |
| [:punct:]  |    既不在[:alnum:]又不在[:cntrl:]中的任意字符     |
| [:space:]  | 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]） |
| [:upper:]  |              任意大写字母（同[A-Z]）              |
| [:xdigit:] |         任意十六进制数字（同[a-fA-F0-9]）         |

**匹配多个实例**

正则表达式重复元字符

| 元 字 符 |            说 明             |
| :------: | :--------------------------: |
|    *     |        0个或多个匹配         |
|    +     |  1个或多个匹配（等于{1,}）   |
|    ?     |  0个或1个匹配（等于{0,1}）   |
|   {n}    |        指定数目的匹配        |
|   {n,}   |     不少于指定数目的匹配     |
|  {n, m}  | 匹配数目的范围（m不超过255） |

**定位符**

为了匹配特定位置的文本，需要使用定位符。

| 元 字 符 |   说 明    |
| :------: | :--------: |
|    ^     | 文本的开始 |
|    $     | 文本的结尾 |
| [[:<:]]  |  词的开始  |
| [[:>:]]  |  词的结尾  |

`LIKE` 和 `REGEXP` 的不同在于，`LIKE` 匹配整个串而 `REGEXP` 匹配子串。利用定位符，通过用 `^` 开始每个表达式，用`$` 结束每个表达式，可以使 `REGEXP` 的作用与 `LIKE` 一样



## 创建计算字段

计算字段并不实际存在于数据库表中。计算字段是运行时在SELECT语句 内创建的。

字段（field） 基本上与列（column）的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。

**拼接字段**

拼接（concatenate） 将值联结到一起构成单个值

使用 `Concat()` 函数来拼接两个列

Concat() 拼接串，即把多个串连接起来形成一个较长的串。 Concat()需要一个或多个指定的串，各个串之间用逗号分隔。

Trim函数 MySQL除了支持 `RTrim()`（去掉串右边的空格），还支持 `LTrim()`（去掉串左边的空格）以及 `Trim()`（去掉串左右两边的空格）。

**使用别名**

SQL支持列别名。别名（alias）是一个字段或值的替换名。别名用 `AS` 关键字赋予。

导出列  别名有时也称为导出列（derived column），不管称为什么，它们所代表的都是相同的东西。

**执行算术计算**

MySQL支持以下基本算术操作符。此外，圆括号可用来区分优先顺序。

| 操 作 符 | 说 明 |
| :------: | :---: |
|    +     |  加   |
|    -     |  减   |
|    *     |  乘   |
|    、    |  除   |



## 使用数据处理函数

**函数**

SQL支持利用函数来处理数据。函数 一般是在数据上执行的，它给数据的转换和处理提供了方便。

**文本处理函数**

|    函 数    |       说 明       |
| :---------: | :---------------: |
|   Left()    | 返回串左边的字符  |
|  Length()   |   返回串的长度    |
|  Locate()   | 找出串的一个子串  |
|   Lower()   |  将串转换为小写   |
|   LTrim()   | 去掉串左边的空格  |
|   Right()   | 返回串右边的字符  |
|   RTrim()   | 去掉串右边的空格  |
|  Soundex()  | 返回串的SOUNDEX值 |
| SubString() |  返回子串的字符   |
|   Upper()   |  将串转换为大写   |

SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。

**日期和时间处理函数**

日期和时间采用相应的数据类型和特殊的格式存储，以便能快速和有效地排序或过滤，并且节省物理存储空间.

一般，应用程序不使用用来存储日期和时间的格式，因此日期和时间函数总是被用来读取、统计和处理这些值。由于这个原因，日期和时间函数在MySQL语言中具有重要的作用。

|     函 数     |             说 明              |
| :-----------: | :----------------------------: |
|   AddDate()   |    增加一个日期（天、周等）    |
|   AddTime()   |    增加一个时间（时、分等）    |
|   CurDate()   |          返回当前日期          |
|   CurTime()   |          返回当前时间          |
|    Date()     |     返回日期时间的日期部分     |
|  DateDiff()   |        计算两个日期之差        |
|  Date_Add()   |     高度灵活的日期运算函数     |
| Date_Format() |  返回一个格式化的日期或时间串  |
|     Day()     |     返回一个日期的天数部分     |
|  DayOfWeek()  | 对于一个日期，返回对应的星期几 |
|    Hour()     |     返回一个时间的小时部分     |
|   Minute()    |     返回一个时间的分钟部分     |
|    Month()    |     返回一个日期的月份部分     |
|     Now()     |       返回当前日期和时间       |
|   Second()    |      返回一个时间的秒部分      |
|    Time()     |   返回一个日期时间的时间部分   |
|    Year()     |     返回一个日期的年份部分     |



**数值处理函数**

数值处理函数仅处理数值数据。这些函数一般主要用于代数、三角 或几何运算，因此没有串或日期—时间处理函数的使用那么频繁。

| 函 数  |       说 明        |
| :----: | :----------------: |
| Abs()  | 返回一个数的绝对值 |
| Cos()  | 返回一个角度的余弦 |
| Exp()  | 返回一个数的指数值 |
| Mod()  |  返回除操作的余数  |
|  Pi()  |     返回圆周率     |
| Rand() |   返回一个随机数   |
| Sin()  | 返回一个角度的正弦 |
| Sqrt() | 返回一个数的平方根 |
| Tan()  | 返回一个角度的正切 |



## 汇总数据

**聚集函数**

我们经常需要汇总数据而不用把它们实际检索出来，为此MySQL提供了专门的函数。使用这些函数，MySQL查询可用于检索数据，以便分 析和报表生成。

**聚集函数**（aggregate function） 运行在行组上，计算和返回单个值的函数。

|  函 数  |      说 明       |
| :-----: | :--------------: |
|  AVG()  | 返回某列的平均值 |
| COUNT() |  返回某列的行数  |
|  MAX()  | 返回某列的最大值 |
|  MIN()  | 返回某列的最小值 |
|  SUM()  |  返回某列值之和  |

**聚集不同值**

以上5个聚集函数都可以如下使用：

- 对所有的行执行计算，指定ALL参数或不给参数（因为ALL是默认 行为）
- 只包含不同的值，指定DISTINCT参数。

使用了 `DISTINCT` 参数，平均值只考虑各个不同的价格

注意：如果指定列名，则DISTINCT只能用于COUNT()。DISTINCT 不能用于COUNT(*)，因此不允许使用COUNT（DISTINCT）， 否则会产生错误。类似地，DISTINCT必须使用列名，不能用于计算或表达式。

**组合聚集函数**

`SELECT` 语句可根据需要包含多个聚集函数，使用 `,`分隔。



## 分组数据

**数据分组**

分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。

**创建分组**

分组是在 `SELECT` 语句的 `GROUP BY` 子句中建立的。

`select [columns] ... from [tables] group by [columns]`

GROUP BY子句指示MySQL分组数据，然后对每个组而不是 整个结果集进行聚集。

- GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套， 为数据分组提供更细致的控制。
-  如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算 （所以不能从个别的列取回数据）。
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式 （但不能是聚集函数）。如果在SELECT中使用表达式，则必须在 GROUP BY子句中指定相同的表达式。不能使用别名。
- 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子 句中给出。
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列 中有多行NULL值，它们将分为一组。
- GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。

`with rollup`：使用WITH ROLLUP关键字，可以得到每个分组以及每个分组汇总级别（针对每个分组）的值。

**过滤分组**

除了能用GROUP BY分组数据外，MySQL还允许过滤分组，规定包括 哪些分组，排除哪些分组。

`HAVING`：过滤分组，HAVING支持所有WHERE操作符。

HAVING和WHERE的差别：这里有另一种理解方法，WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤。这是一个重要的区别，WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

**分组和排序**

|                   ORDER BY                    |                         GROUP BY                         |
| :-------------------------------------------: | :------------------------------------------------------: |
|                排序产生的输出                 |             分组行。但输出可能不是分组的顺序             |
| 任意列都可以使用（甚至 非选择的列也可以使用） | 只可能使用选择列或表达式列，而且必须使用每个选择列表达式 |
|                  不一定需要                   |     如果与聚集函数一起使用列（或表达式），则必须使用     |

**SELECT子句顺序**

|  子 句   |       说 明        |      是否必须使用      |
| :------: | :----------------: | :--------------------: |
|  SELECT  | 要返回的列或表达式 |           是           |
|   FROM   |  从中检索数据的表  | 仅在从表选择数据时使用 |
|  WHERE   |      行级过滤      |           否           |
| GROUP BY |      分组说明      | 仅在按组计算聚集时使用 |
|  HAVING  |      组级过滤      |           否           |
| ORDER BY |    输出排序顺序    |           否           |
|  LIMIT   |    要检索的行数    |           否           |



## 使用子查询

**子查询**

查询（query） 任何SQL语句都是查询。但此术语一般指 SELECT 语句。

SQL还允许创建子查询（subquery），即嵌套在其他查询中的查询

**利用子查询进行过滤**

列必须匹配 在WHERE子句中使用子查询（如这里所示），应 该保证SELECT语句具有与WHERE子句中相同数目的列。通常， 子查询将返回单个列并且与单个列匹配，但如果需要也可以使用多个列。

虽然子查询一般与IN操作符结合使用，但也可以用于测试等于（=）、 不等于（<>）等。

**作为计算字段使用子查询**

使用子查询的另一方法是创建计算字段。



## 联结表

SQL最强大的功能之一就是能在数据检索查询的执行中联结（join）表。联结是利用SQL的SELECT能执行的最重要的操作。

**关系表**

**外键**（foreign key） 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。

关系数据可以有效地存储和方便地处理。因此，关系数据库的可伸缩性远比非关系数据库要好。

**可伸缩性**（scale） 能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称之为可伸缩性好（scale well）。

**完全限定列名** 在引用的列可能出现二义性时，必须使用完全限定列名（用一个点分隔的表名和列名）。如果引用一个没有用表名限制的具有二义性的列名，MySQL将返回错误。

不要忘了WHERE子句 应该保证所有联结都有WHERE子句，否则MySQL将返回比想要的数据多得多的数据。同理，应该保证WHERE子句的正确性。不正确的过滤条件将导致MySQL返回不正确的数据

**笛卡儿积**（cartesian product） 由没有联结条件的表关系返回的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘 以第二个表中的行数。

**目前为止所用的联结称为等值联结（equijoin），它基于两个表之间的相等测试。这种联结也称为内部联结。其实，对于这种联结可以使用稍微不同的语法来明确指定联结的类型。**

`from table1, table2 ` -> `from table1 inner join table2 on ...`



## 创建高级联结

**使用表别名**

表别名不仅能用于WHERE子句，它还可以用于SELECT的列表、ORDER BY子句 以及语句的其他部分。

表别名只在查询执行中使用。与列别名不一样，表别名 不返回到客户机。

**使用不同类型的联结**

自联结、自然联结和外部联结

**自联结**

自联结通常作为外部语句用来替代 从相同表中检索数据时使用的子查询语句。虽然最终的结果是 相同的，但有时候处理联结远比处理子查询快得多。

**自然联结**

无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被联结的列）。标准的联结（前一章中介绍的内部联结）返回所有数据，甚至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次。

**外部联结**

联结包含了那些在相关表中没有关联行的行。这种类型的联结称为外部联结。

在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而 LEFT 指出的是OUTER JOIN左边的表）。

左外部联结和右外部联结。它们之间的唯一差别是所关联的表的顺序不同。换句话说，左外部联结可通过颠倒FROM或WHERE子句中表的顺序转换为右外部联结。因此，两种类型的外部联结可互 换使用，而究竟使用哪一种纯粹是根据方便而定。

**使用带聚集函数的联结**

聚集函数可以与其他联结一起使用

**使用联结和联结条件**

- 注意所使用的联结类型。一般我们使用内部联结，但使用外部联 结也是有效的。
- 保证使用正确的联结条件，否则将返回不正确的数据。
- 应该总是提供联结条件，否则会得出笛卡儿积。
- 在一个联结中可以包含多个表，甚至对于每个联结可以采用不同 的联结类型。虽然这样做是合法的，一般也很有用，但应该在一起测试它们前，分别测试每个联结。这将使故障排除更为简单。



## 组合查询

**组合查询**

多数SQL查询都只包含从一个或多个表中返回数据的单条SELECT语 句。MySQL也允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（union）或复合查询 （compound query）。

有两种基本情况，其中需要使用组合查询:

- 在单个查询中从不同的表返回类似结构的数据;
- 对单个表执行多个查询，按单个查询返回数据.

**创建组合查询**

可用UNION操作符来组合数条SQL查询。利用UNION，可给出多条SELECT语句，将它们的结果组合成单个结果集。

**使用UNION**

`UNION` 的使用很简单。所需做的只是给出每条SELECT语句，在各条语句之间放上关键字UNION。

**UNION规则**

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个UNION关键字）。
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。

**包含或取消重复的行**

UNION从查询结果集中自动去除了重复的行（换句话说，它的行为与 单条SELECT语句中使用多个WHERE子句条件一样）

这是UNION的默认行为，但是如果需要，可以改变它。事实上，如果 想返回所有匹配行，可使用UNION ALL而不是UNION。

**对组合查询结果排序**

SELECT语句的输出用ORDER BY子句排序。在用UNION组合查询时，只能使用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后。对于结果集，不存在用一种方式排序一部分，而又用另一种方式排序另一 部分的情况，因此不允许使用多条ORDER BY子句。