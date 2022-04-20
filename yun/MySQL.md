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
    ```

    