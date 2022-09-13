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





## SELECT语句

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

**排序数据**

`select [columns] from [tables] order by [columns]`:ORDER BY子句对输出排序

**按多个列排序**

`select [columns1, columns2, ...] from [tables] order by [columns3, columns4]`：检索1，2按3，4排序

**指定排序方向**

`select [columns1, columns2, ...] from [tables] order by [columns] DESC`：DESC降序



## 过滤数据

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

