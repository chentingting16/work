# InnoDB记录存储结构

## InnoDB页

* 将数据划分为若干个数据页，以页作为磁盘和内存之间交互的基本单位；
* InnoDB中页的大小一般为16KB，系统变量innodb_page_size默认为16384(B)，即16KB。该变量只能在第一次初始化数据目录时指定，之后不能更改，因此不能在服务器运行过程中修改页面大小。

## InnoDB行格式

1. innodb一共有四种行格式，分别是：compact、redundant、dynamic、compressed。
2. 基本时记录的**额外信息**和记录的**真实数据**两大部分。

```mysql
create table test（
 c1 varchar(10),
 c2 varchar(10) not null,
 c3 char(10),
 c4 varchar(10),
）
```

### COMPACT

![img](https://img-blog.csdnimg.cn/e3759df095c84715bfcb8f7a607833a0.png)

#### 1、额外信息：

1. 变长字段宽度列表（逆序）---真实数据占用的字节数

​	1）插入一条记录`insert into test(c2,c3,c4) value(bb,ccc,dddd)`，在**[变长字段宽度列表]**中就会存储c4、c2字段的长度，列表中为4 2（注意：是逆序存放）；

​	2）变长字段长度列表只存储值为非NULL的列的内容长度，不存储值为NULL的列的内容长度(放在null值列表中)；

​	3）如果该变长字段允许存储的最大字节数(M*W)>255，并且真实数据占用字节数L超过127，则使用2个字节表示真实占用的字节数，否则用一个字节。

​	注：若某个字段占用字节特别多，InnoDB可能把该该字段值的一部分数据放到溢出页中。

2. null值列表（逆序）

​	1）插入一行数据前，MySQL先检查表的结构，查看哪些列可以为空，然后对这行数据中这些可以为空的列，检查他们的值是否为空，如果为空则用 1 标识，如果不为空则用 0 标识
​	如：`insert into test(c1,c2) value(a,bb)`，这条数据只有c1，c2列不为空，字节高位补零+二进制位按列的顺序逆序排列，位`00000(110)`

3. 记录头信息

​	记录头信息由固定的5字节(40个二进制位)组成，用于描述记录的一些属性。

#### 2、记录的真实数据

1. row_id
   建表时若没有指定主键，并且表中又找不出非空且不重复的列的话，那么MySQL会自动为该表生成一个隐藏的有序的列作为主键列，这个列就是row_id，用6个字节表示（所以该ID能表示的范围就是1 ~ 2 ^48 ）。如果有指定主键，那么MySQL的行记录中不会有这一列。
2. trx_id：记录着最近修改这条数据的事务ID
3. roll_pointer：主要是为innodb的mvcc生成版本连使用
4. 其他列数据：后面的就是存放我们具体插入的列的数据了，没有什么特殊的

# InnoDB数据页存储结构

[MySQL之数据页结构 - 墨天轮 (modb.pro)](https://www.modb.pro/db/139052)

## 页类型

* 页是InnoDB管理存储空间的基本单位，一个页的大小一般是16KB。
* InnoDB设计了多种类型的页，如存放表空间头部信息的页、存放Change Buffer的页、存放undo日志的页等...
* 存放记录的页官方成为索引页(INDEX)页， 也叫数据页。

## 数据页结构

![image-20220428160644872](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428160644872.png)

![image-20220428160726104](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428160726104.png)

* 向数据页中插入记录

![image-20220428161755049](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428161755049.png)

![image-20220428161724790](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428161724790.png)

![image-20220428165058137](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428165058137.png)

* 页目录

![image-20220428165130428](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428165130428.png)

![image-20220428165206903](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428165206903.png)

![image-20220428165227603](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428165227603.png)

![image-20220428165248981](C:\Users\ctt\AppData\Roaming\Typora\typora-user-images\image-20220428165248981.png)

# 索引结构

