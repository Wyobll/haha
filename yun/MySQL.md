# MySQL

* 数据库：存储数据的仓库，数据是有组织的进行存储
* 数据库管理系统：操纵和管理数据库的大型软件
* SQL：操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准

#### 启动

命令窗口：输入 services.msc

!["C:\Users\lx\Pictures\资料\mysql.png"](C:\Users\lx\Pictures\资料\mysql.png)



启动：net start mysql80

停止：net stop mysql80

![image-20220420204059637](C:\Users\lx\Pictures\资料\mysql2.png)



配置MySQL环境变量在我的电脑中高级配置环境变量path中添加MySQL路径

MySQL Server 8.0/bin



#### MySQL数据库

* 关系型数据库（RDBMS）

建立在关系模型基础上，由多张相互连接的二维表组成的数据库



#### SQL



> * **SQL通用语法**
>
> 1.SQL语句可以单行或多行书写，以分号结尾
>
> 2.SQL语句可以使用空格/缩进来增强语句的可读性
>
> 3.MySQL数据库的SQL语句不区分大小写，关键字建议使用大写
>
> 4.注释：  单行注释：--或#  多行注释：/* 内容 */



> * **SQL分类**
>
> DDL：数据定义语言，定义数据库对象
>
> DML：数据操作语言，对数据库表中数据进行增删改
>
> DQL：数据查询语言，查询数据库中表的记录
>
> DCL：数据控制语言，创建数据库用户、控制数据库的访问权限



* **DDL**

  * 查询

    查询所有数据库

    ```shell
    show databases;
    ```

    查询当前数据库

    ```shell
    select databases();
    ```

  * 创建

    ```shell
    create database 数据库名 字符集 排序规则；
    ```

  * 删除

    ```shell
    drop database 数据库名
    ```

  * 使用

    ```shell
    use 数据库名；
    ```

    

  表操作

  * 查询当前数据库所有表

    ```shell
    show tables;
    ```

  * 查询表结构

    ```shell
    desc 表名；
    ```

  * 查询指定表的建表语句

    ```shell
    show create table 表名；
    ```

  * 创建表

    ```shell
    create table 表名（
    ... ,
    ... ,
    ...最后一个字段没有参数，
    ）；
    ```

    

  **1、整型**

  | MySQL数据类型 | 含义（有符号）                       |
  | ------------- | ------------------------------------ |
  | tinyint(m)    | 1个字节 范围(-128~127)               |
  | smallint(m)   | 2个字节 范围(-32768~32767)           |
  | mediumint(m)  | 3个字节 范围(-8388608~8388607)       |
  | int(m)        | 4个字节 范围(-2147483648~2147483647) |
  | bigint(m)     | 8个字节 范围(+-9.22*10的18次方)      |

  取值范围如果加了unsigned，则最大值翻倍，如tinyint unsigned的取值范围为(0~256)。

  int(m)里的m是表示SELECT查询结果集中的显示宽度，并不影响实际的取值范围，没有影响到显示的宽度，不知道这个m有什么用。

  **2、浮点型(float和double)**

  | MySQL数据类型 | 含义                                          |
  | ------------- | --------------------------------------------- |
  | float(m,d)    | 单精度浮点型 8位精度(4字节) m总个数，d小数位  |
  | double(m,d)   | 双精度浮点型 16位精度(8字节) m总个数，d小数位 |

  设一个字段定义为float(6,3)，如果插入一个数123.45678,实际数据库里存的是123.457，但总个数还以实际为准，即6位。整数部分最大是3位，如果插入数12.123456，存储的是12.1234，如果插入12.12，存储的是12.1200.

  **3、定点数**

  浮点型在数据库中存放的是近似值，而定点类型在数据库中存放的是精确值。

  decimal(m,d) 参数m<65 是总个数，d<30且 d<m 是小数位。

  **4、字符串(char,varchar,_text)**

  | MySQL数据类型 | 含义                            |
  | ------------- | ------------------------------- |
  | char(n)       | 固定长度，最多255个字符         |
  | varchar(n)    | 固定长度，最多65535个字符       |
  | tinytext      | 可变长度，最多255个字符         |
  | text          | 可变长度，最多65535个字符       |
  | mediumtext    | 可变长度，最多2的24次方-1个字符 |
  | longtext      | 可变长度，最多2的32次方-1个字符 |

  char和varchar：

  1.char(n) 若存入字符数小于n，则以空格补于其后，查询之时再将空格去掉。所以char类型存储的字符串末尾不能有空格，varchar不限于此。

  2.char(n) 固定长度，char(4)不管是存入几个字符，都将占用4个字节，varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，

  所以varchar(4),存入3个字符将占用4个字节。

  3.char类型的字符串检索速度要比varchar类型的快。
  varchar和text：

  1.varchar可指定n，text不能指定，内部存储varchar是存入的实际字符数+1个字节（n<=255）或2个字节(n>255)，text是实际字符数+2个字

  节。

  2.text类型不能有默认值。

  3.varchar可直接创建索引，text创建索引要指定前多少个字符。varchar查询速度快于text,在都创建索引的情况下，text的索引似乎不起作用。

  **5.二进制数据(_Blob)**

  1._BLOB和_text存储方式不同，_TEXT以文本方式存储，英文存储区分大小写，而_Blob是以二进制方式存储，不分大小写。

  2._BLOB存储的数据只能整体读出。

  3._TEXT可以指定字符集，_BLO不用指定字符集。

  **6.日期时间类型**

  | MySQL数据类型 | 含义                          |
  | ------------- | ----------------------------- |
  | date          | 日期 '2008-12-2'              |
  | time          | 时间 '12:25:36'               |
  | datetime      | 日期时间 '2008-12-2 22:06:44' |
  | timestamp     | 自动存储记录修改时间          |

  若定义一个字段为timestamp，这个字段里的时间数据会随其他字段修改的时候自动刷新，所以这个数据类型的字段可以存放这条记录最后被修改的时间。

  数据类型的属性

  | MySQL关键字        | 含义                     |
  | ------------------ | ------------------------ |
  | NULL               | 数据列可包含NULL值       |
  | NOT NULL           | 数据列不允许包含NULL值   |
  | DEFAULT            | 默认值                   |
  | PRIMARY KEY        | 主键                     |
  | AUTO_INCREMENT     | 自动递增，适用于整数类型 |
  | UNSIGNED           | 无符号                   |
  | CHARACTER SET name | 指定一个字符集           |

  **二、MYSQL数据类型的长度和范围**

  各数据类型及字节长度一览表：

  | 数据类型           | 字节长度 | 范围或用法                                                   |
  | ------------------ | -------- | ------------------------------------------------------------ |
  | Bit                | 1        | 无符号[0,255]，有符号[-128,127]，天缘博客备注：BIT和BOOL布尔型都占用1字节 |
  | TinyInt            | 1        | 整数[0,255]                                                  |
  | SmallInt           | 2        | 无符号[0,65535]，有符号[-32768,32767]                        |
  | MediumInt          | 3        | 无符号[0,2^24-1]，有符号[-2^23,2^23-1]]                      |
  | Int                | 4        | 无符号[0,2^32-1]，有符号[-2^31,2^31-1]                       |
  | BigInt             | 8        | 无符号[0,2^64-1]，有符号[-2^63 ,2^63 -1]                     |
  | Float(M,D)         | 4        | 单精度浮点数。天缘博客提醒这里的D是精度，如果D<=24则为默认的FLOAT，如果D>24则会自动被转换为DOUBLE型。 |
  | Double(M,D)        | 8        | 双精度浮点。                                                 |
  | Decimal(M,D)       | M+1或M+2 | 未打包的浮点数，用法类似于FLOAT和DOUBLE，天缘博客提醒您如果在ASP中使用到Decimal数据类型，直接从数据库读出来的Decimal可能需要先转换成Float或Double类型后再进行运算。 |
  | Date               | 3        | 以YYYY-MM-DD的格式显示，比如：2009-07-19                     |
  | Date Time          | 8        | 以YYYY-MM-DD HH:MM:SS的格式显示，比如：2009-07-19 11：22：30 |
  | TimeStamp          | 4        | 以YYYY-MM-DD的格式显示，比如：2009-07-19                     |
  | Time               | 3        | 以HH:MM:SS的格式显示。比如：11：22：30                       |
  | Year               | 1        | 以YYYY的格式显示。比如：2009                                 |
  | Char(M)            | M        | 定长字符串。                                                 |
  | VarChar(M)         | M        | 变长字符串，要求M<=255                                       |
  | Binary(M)          | M        | 类似Char的二进制存储，特点是插入定长不足补0                  |
  | VarBinary(M)       | M        | 类似VarChar的变长二进制存储，特点是定长不补0                 |
  | Tiny Text          | Max:255  | 大小写不敏感                                                 |
  | Text               | Max:64K  | 大小写不敏感                                                 |
  | Medium Text        | Max:16M  | 大小写不敏感                                                 |
  | Long Text          | Max:4G   | 大小写不敏感                                                 |
  | TinyBlob           | Max:255  | 大小写敏感                                                   |
  | Blob               | Max:64K  | 大小写敏感                                                   |
  | MediumBlob         | Max:16M  | 大小写敏感                                                   |
  | LongBlob           | Max:4G   | 大小写敏感                                                   |
  | Enum               | 1或2     | 最大可达65535个不同的枚举值                                  |
  | Set                | 可达8    | 最大可达64个不同的值                                         |
  | Geometry           |          |                                                              |
  | Point              |          |                                                              |
  | LineString         |          |                                                              |
  | Polygon            |          |                                                              |
  | MultiPoint         |          |                                                              |
  | MultiLineString    |          |                                                              |
  | MultiPolygon       |          |                                                              |
  | GeometryCollection |          |                                                              |

  

  **三、使用建议**

  1、在指定数据类型的时候一般是采用从小原则，比如能用TINY INT的最好就不用INT，能用FLOAT类型的就不用DOUBLE类型，这样会对MYSQL在运行效率上提高很大，尤其是大数据量测试条件下。

  2、不需要把数据表设计的太过复杂，功能模块上区分或许对于后期的维护更为方便，慎重出现大杂烩数据表

  3、数据表和字段的起名字也是一门学问

  4、设计数据表结构之前请先想象一下是你的房间，或许结果会更加合理、高效

  5、数据库的最后设计结果一定是效率和可扩展性的折中，偏向任何一方都是欠妥的



表操作-修改

* DDL

  * 添加字段

    ```shell
    alter table 表名 add 字段名 类型（长度）[comment 注释][约束]；
    ```

  * 修改字段名和字段类型

    ```shell
    alter table 表名 change 旧字段名 新字段名 类型（长度）[comment 注释][约束]；
    ```

  * 删除字段

    ```shell
    alter table 表名 drop 字段名；
    ```

  * 修改表名

    ```shell
    alter table 表名 rename to 新表名；
    ```

    

表操作-删除

* DDL

  * 删除表

    ```shell
    drop table [if exists] 表名；
    ```

  * 删除指定表，并重新创建该表

    ```shell
    truncate table 表名；
    ```

    

#### DML

数据操作语言，用来对数据库中表的数据记录进行增删改操作

> 增加数据（insert）
>
> 修改数据（update）
>
> 删除数据（delete）



* DML-添加数据

  * 给指定字段添加数据

    ```shell
    insert into 表名（字段名1,字段名2,...) values(值1，值2，...)
    ```

  * 给全部字段添加数据

    ```shell
    insert into 表名 values(值1,值2...)
    ```

  * 批量添加数据

    ```shell
    insert into 表名（字段名1,字段名2,...) values(值1，值2，...)，(值1，值2，...)，(值1，值2，...)
    ```

    ```shell
    insert into 表名 values(值1,值2...),(值1，值2，...),(值1，值2，...)
    ```

    注意：

    * 插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
    * 字符串和日期型数据应该包含在引号中。
    * 插入的数据大小，应该在字段的规定范围内。



* DML-修改数据
  
  * 
  
    ```shell
    update 表名 set 字段名1=值1，字段2=值2，...[where 条件]；
    ```
  
    

* DML-删除数据

  * 

    ```shell
    delete from 表名 [where 条件]
    ```





**常用参数:**

> -A 不预读数据库信息，提高连接和切换数据库速度,使用--disable-auto-rehash代替
> --default-character-set  使用的默认字符集
> -e 执行命令并退出
> -h 主机地址
> -p 连接到服务器时使用的密码
> -P 连接的端口号



**change和modify:**

> 1 前者可以修改列名称,后者不能. 
> 2 change需要些两次列名称.



**字段增加修改 add/change/modify/ 添加顺序:**

> 1 add 增加在表尾.
> 2 change/modify 不该表字段位置.
> 3 修改字段可以带上以下参数进行位置调整(frist/after column_name);
>
> alter table emp change age age int(2) after ename;
> alter table emp change age age int(3) first;



* DQL-基本查询

  * 查询多个字

  ```shell
  select 字段1，字段2，字段3...from 表名；
  ```

  ```shell
  select * from 表名；
  ```

  * 设置别名

    ````shell
    select 字段1 [as 别名1]，字段2 [as 别名2]...from 表名；
    ````

  * 去除重复记录

    ```shell
    select distinct 字段列表 from表名；
    ```

  DQL-条件查询

  * 

    ```shell
    select 字段列表 from 表名 where 条件列表；
    ```

  DQL-聚合函数

  * | 函数  | 功能     |
    | ----- | -------- |
    | count | 统计数量 |
    | max   | 最大值   |
    | min   | 最小值   |
    | avg   | 平均值   |
    | sum   | 求和     |

  * 

    ```shell
    select 聚合函数(字段列表) from 表名；
    ```

    注意：null值不参与所有聚合函数运算

  DQL-分组查询

  * 

    ```shell
    select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件]；
    ```

  ​    

  列：根据性别分组统计男性员工和女性员工的数量

     **select gender，count(*) from wy group by gender;**

  

  where和having的区别：

  > 执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
  >
  > 判断条件不同：where不能对聚合函数进行判断，而having可以。

  

  注意： 

  执行顺序：where>聚合函数>having

  分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。

  

  > select 字段列表
  >
  > from 表名列表
  >
  > where 条件列表
  >
  > group by 分组字段列表
  >
  > having 分组后条件列表
  >
  > order by 排序字段列表
  >
  > limit 分页参数

   DQL-排序查询

```sgell
select 字段列表 from 表名 order by 字段1 排序方式1，字段2 排序方式2；
```

排序方式

ASC：升序asc 可以省略默认排序

DESC：降序desc

​    DQL-分页查询

```shell
select 字段列表 from 表名 limit 起始索引，查询记录数；
```

 

​    注意：

   起始索引从0开始，起始索引=(查询页码-1)*每页显示记录数

   分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是limit

   如果查询的是第一页数据，起始索引可以省略，直接简写为limit10



* DCL-管理用户

  * 查询用户

    ```shell
    use mysql;
    select * from user;
    ```

  * 创建用户

    ```shell
    create user '用户名'@'主机名' identified by '密码'；
    ```

  * 修改用户密码

    ```shell
    alter user '用户名'@'主机名' identified with mysql_native_password by '新密码'；
    ```

  * 删除用户

    ```shell
    drop user '用户名'@'主机名';
    ```

    

* DCL-权限控制

  * 查询权限

    ```shell
    show grants for '用户名'@'主机名'；
    ```

  * 授予权限

    ```shell
    grant 权限列表 on 数据库名 表名 to '用户名'@'主机名'；
    ```

  * 撤销权限

    ```shell
    revoke 权限列表 on 数据库名 表名 from '用户名'@'主机名'；
    ```

    

| 权限                | 说明               |
| ------------------- | ------------------ |
| all，all privileges | 所有权限           |
| select              | 查询数据           |
| insert              | 插入数据           |
| update              | 修改数据           |
| delete              | 删除数据           |
| alter               | 修改表             |
| drop                | 删除数据库/表/视图 |
| create              | 创建数据库/表      |





**float , double , decimal 特点:**

> 1.(m,d)表示方式:m指的是整数位,d指的是小数位(又称作精度和标度)
> 2.float/double四舍五入丢失精度,decimal会截断数据并输出warning
> 3.如果不指定精度,float/double采用操作系统默认,decimal则是(10,0)



**timestamp和datetime区别:**

> - timestamp支持范围小(1970-01-01 08:00:01到2038年某个点)
> - 表中第一个timestamp字段,会默认采用当前系统时间.如果更新其他字段,该字段没有赋值的话,则该字段会自动更新.如果指定字段不满足规格,则采用零值填充
> - timestamp查询和插入都会受到当地时区影响
> -  datetime支持范围宽度大(1000-01-01 00:00:00到9999-12-31 23:23:59)



