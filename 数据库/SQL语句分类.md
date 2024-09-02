
1. **DDL     数据定义语言**
   - *对数据库对象进行操作的语言。*
     - 数据库，表，视图，索引等。
   - CREAT
   - DROP
   - ALTER

2. **DML    数据操作语言**
   - *对表中的数据进行操作的语言。*
   - INSERT
   - UPDAT
   - DELETE

3. **DQL     数据查询语言**
   - *对表中的数据进行查询的语言。*
   - SELECT

4. **DCL     数据控制语言**
   - *DBA用于管理数据库的语言。*

5. **TCL     事物控制语言**
   - *对DML数据操作保证具有原子性、一致性等。*
   - COMMIT
   - ROLLBACK


### 1、DDL     数据定义语言
   - **查看DBMS中已有的数据库：**
     ```sql
      SHOW DATABASES;
     ```
   
   - **新建一个数据库：**
     ```sql
      CREATE DATABASE 数据库名;
     ```
     例如：
     ```sql
      CREATE DATABASE mydb;
     ```
   
   - **创建数据库时可以指定字符集：**
     ```sql
      CREAT DATABASE 数据库名 CHARSET=UTF8;     // 默认为 utf8mb3
      CHARSET=UTF9MB4    // 但更推荐 utf8mb4，因为可以表示一些生僻字和emoji表情
     ```
     例如：
     ```sql
      CREAT DATABASE mydb1 CHRESET=UTF8
     ```
   
   - **查看指定的数据库信息：**
     ```sql
      SHOW CREATE DATABASE 数据库名;
     ```
     例如：
     ```sql
      SHOW CREATE DATABASE mydb;
     ```

   - **删除数据库：**
     ```sql
      DRCP DATABASE 数据库名;
     ```
     例如：
     ```sql
      DRCP DATABASE mydb1
     ```

   - **使用一个数据库 / 切换到数据库：**
     ```sql
	  USE 数据库名
     ```
     *若希望保存数据，则数据必须保存在某张表上。*
     而表必须保存在某个数据库上，*因此后期为了对某个数据库的表进行操作，要先切换到该数据库上*，才能进行操作。



#### 1.1、表 相关操作
   - **创建表：**
     语法：
     ```sql
      CREAT TABLE 表名(
      字段名 类型,
      字段名 类型,
      ...
      )
     ```
      例：
      ```sql
       CREAT TABLE user(
       id INT,                    // INT在mysql中为整数类型，
       userName VARCHAR(30),      // VARCHAR在mysql中为字符串类型，长度为字节。
       password VARCHAR(30),      // VARCHAR(30)则字符转换的 字节最多30个，
       nickname VARCHAR(30),         若UTF-8编码则 中文最多10个字(每个中文 3字节)
       age INT(30)                // INT指定长度为 位数，INT(3)为最多 3位整数
       )
      ```

   - **查看已创建的表的 表结构：**
     ```sql
      DESC 表名;
     ```
     例如：
     ```sql
      DESC user;
	 ```

   - **查看已创建表的详细信息：**
     ```sql
      SHOW CREAT TABLE 表名;
     ```
     例如：
     ```sql
      SHOW CREAT TABLE user;
     ```

   - **删除表：**
     ```sql
      DROP TABLE 表名;
      DROP TABLE IF EXISTS 表名；    // 如果该表存在就删除
	 ```

   - **查看数据库中所有的表**
     ```sql
      SHOW TABLES;
     ```

   - **修改表：**
     - *修改表名：*
       ```sql
      RENAME TABLE 原表名 TO 新表名;
       ```
       例如：
       ```sql
       将表user修改为userinfo
       RENAME TABLE user TO userinfo;
	   ```
	
     - *修改表结构：*
       `ALTER TABLE`
       
       - **添加 字段（列）：**
          1. *向表末尾  追加新的字段：*
            ```sql
            ALTER TABLE 表名 ADD 列名 类型;
            ```
            例如：
            ```sql
            ALTER TABLE hero ADD gender VARCHAR(10);
            ```
          2. *将字段（列）添加到表的 第一个字段位置上*
            ```sql
            ALTET TABLE 表名 ADD 字段名 类型 FIRST;
            ```
            例如：
            ```sql
            在hero表的第一个字段上添加id
            ALTER TABLE hero ADD id INT(10) FIRST;
            ```
          3. *将字段 插入到表中某个位置（某个字段后面）*
             - 只能插入到某个字段后面，不能插入到前面。
            ```sql
            ALTER TABLE 表名 ADD 字段名 类型 AFTER 表中某字段;
            ```
            例如：
            ```sql
            ALTER TABLE hero ADD password VARCHAR(30) AFTER name;
            ```
       
       - **删除 字段（列）**
         ```sql
         ALTER TABLE 表名 DROP 字段名;
         ```
         例如：
         ```sql
         将password字段从hero表中删除
         ALTER TABLE hero DROP password;
         ```
     
       - **修改 现有字段（列）**
         - *注意事项：*
           - 修改表字段应当*在表中没有数据时进行*。
             - 如果存在数据，则修改字段可能不成功。（*字段类型、字段长度 等 不兼容的问题*）
             - 比如：表中有10条数据，每条数据gender字段已经有值（每个人的性别）
                            若此时将表字段gender修改类型为INT。此时 字符串转换INT（"男" -> ???）
                            或将字段password的由VARCHAR(10)改为VARCHAR(5)，密码长度可能不兼容
         
         ```sql
         ALTER TABLE 表名 CHANGE 原字段名 新字段名 类型;
         ```
         例1：
         ```sql
         将hero表中age字段的长度改为5
         ALTER TABLE hero CHANGE age age INT(5);
         ```
         例2：
         ```sql
         将hero表中age字段的类型改为VARCHAR(10)
         ALTER TABLE hero CHANGE age age VARCHAR(10);
         ```
         例3：
         ```sql
         将hero表中age字段的名字改为password
         ALTER TABLE hero CHANGE age password VARCHAR(10);
         ```



### 2、DML   数据操作语言

***它是对表中数据进行操作的语言，涵盖操作：增(INSERT)、删(DELETE)，改(UPDATE)***

- 应该在**插入单行**的时候使用 VALUES，在**插入多行**的时候使用 VALUE
    - 插入*单行*时，使用 *VALUES比较快*。
    - 插入*多行*时，使用 *VALUE比较快*。

- **查看表中数据**
    - 只适合在数据库中直接查看使用，使用Java来查看则不建议。
  ```sql
  SELECT * FROM 表名
  ```

- **插入数据( INSERT  INTO )**
    - *语法：*
        ```sql
        INSERT INTO 表名[(字段1,字段2,...)] VALUES(字段1的值, 字段2的值,...);
        
        /*批量插入*/ 
        INSERT INTO 表名[(字段1,字段2,...)] VALUES(字段1的值, 字段2的值,...),(字段1的值, 字段2的值,...);
        ```
        - 注：*在语法上"[ ]"中的内容表示  可写可不写*
        
        - 例：
        ```sql
        INSERT INTO person(name, age) VALUES('张三',25);
        
        /*批量插入*/ 
        INSERT INTO person(name, age) VALUES('张三',25),('李四',26);
        ```
        - 注：
            -  *数据库中字符串的字面量时使用单引号 ( ' ' )  表达的*
            -  *INSERT时指定的字段顺序可以与表不一致*，但是 *VALUE子句中指定的值*  要与  *指定的字段名* ：*个数，顺序，以及类型完全一致*。
    
    - **插入默认值**
        - 当插入数据时不指定某个字段，那么该字段插入默认值。若*创建表时字段没有指定默认值时，默认插入NULL。*
        - 例：
            ```sql
            INSERT INTO person(name) VALUES('王五');
            ```
            - 注：
              - age 字段没有指定，因此插入默认值NULL
              - *数据库中任何字段、任何类型默认值都是NULL*，
              - 如果不想默认值为NULL，也可以在*创建表/修改表*时，*使用DEFAULT指定一个值*。
              ```sql
              修改表中 age的默认值为 20
              ALTER TABLE person CHANGE age age INT(3) DEFAULT 20;
              
              // 修改默认值后 再向表中插入数据，不指定就会默认插入默认值了
              INSERT INTO person(name,age) VALUES('赵六'); //现在赵六的age就是20
              
              提示：也可以在创建person表时为字段指定默认值
              CREAT TABLE person(
	              name VARCHAR(30) DEFAULT '无名',
	              age INT(3) DEFAULT 20
              );
              ```
	
    - **全列插入**
        - 当我们插入数据时 *不指定任何字段名* 时，就是全列插入。
        
        - 此时VALUE子句中*需要为每个字段指定值*。并且要求(个数，顺序，类型*必须与表完全一致*)
        - 若某个字段有*默认值*，可使用 *DEFAULT* 表示默认值。
        - 若某个字段想插入*NULL值*，可以使用 *NULL* 表示。
        - 例：
        ```sql
        INSERT INTO person VALUE('钱七',21);
        
        // 若某个字段有默认值，可以使用 DEFAULT 表示默认值
        INSERT INTO person VALUE('王八',DEAULT);
	    
	    // 若某个字段想插入NULL值，可以用 NULL 表示
	    INSERT INTO person VALUE('王八',NULL)
        
        // 下列为错误示例：
        INSERT INTO person VALUE('钱七');          // 列的值 个数 不匹配
        INSERT INTO person VALUE(23,'钱七');       // 列的值 顺序/类型 不匹配
        ```

- **修改数据( UPDATE  SET )**
    - *1.语法：*
        ```sql
        UPDATE 表名 
        SET 字段1 = 新值1[字段2 = 新值2,...]
        [WHERE 过滤条件];
        ```
        - 注：***UPDATE语句通常都要添加  WHERE字句，用于添加要修改记录的条件，否则全表修改！***
            ```sql
            把表里的age都改成16
            UPDATE person SET age = 16;
            ```
    
    - *2.修改指定记录( 添加WHERE子句 )：*
        - 例：
            ```sql
            将张三的年龄改成20岁
            UPDATE person
            SET age = 20
            WHERE name = '张三';
            
            将person表中年龄为16岁的人改为36岁
            UPDATE person
            SET age = 36
            WHERE age = 16;
            ```
        - *WHERE子句中常用的条件：*
            -  *=，<，<=，>，>=，<>(不等于，!=不是所有的数据库都支持)*
            ```sql
            将person表中年龄大于30岁的人改为29
            UPDATE person
            SET age = 29
            WHERE age>30;
            ```
            - 将一个计算表达式的结果作为值使用：
                ```sql
                将person表中每个人的年龄涨一岁
                UPDATE person
                SET age = age + 1;     // 将每条记录年龄的值+1后再修改原来的值
                ```
            - 同时修改多个字段
                ```sql
                将'李四'改为'李老四'且年龄改为55岁
                UPDATE person
                SET name ='李老四',age = 55
                WHERE name = '李四';
                ```

- **删除语句( DELETE  FROM )**
    - *1.语法：*
        ```sql
        DELETE FROM 表名 [WHERE 过滤条件];
        ```
        - 注：***DELETE语句通常都要添加  WHERE字句，否则是清空表操作。***
        - 例：
            ```sql
            // 清空person表中所有数据的操作
            DELETE FROM person;
            
            // 删除'李老四'
            DELETE FROM person
            WHERE name = '李老四';     // 仅删除person表张满足WHERE条件的记录
            ```
    - *清空表操作：*
        ```sql
        DELETE FROM 表名;
        ```



### 3、DQL    数据查询语言

#### ***DQL是用于查询表中数据的语言。***

- **语法**
    - `SELECT 子句`
    - `FROM 子句`
    - `WHERE 子句`
    - `JOIN...ON... 子句`
    - `GROUP BY 子句`
    - `HAVING 子句`
    - `ORDER BY 子句` 只能是*DQL语句中最后一个子句*。

- **一条DQL语句至少要包含两个字句**，分别是  **SELECT子句  和  FROM子句**
    - SELECT子句：用于指定 *查询的字段名称*，指定的字段会体现在 *查询结果集* 中。
    - FROM子句：用于指定数据 *来自哪些表（指定查询的表）*。


#### **基础查询**

- *由 SELECT 和 FROM 构成。*
- *语法：*
    - SELECT子句  和  FROM子句
        ```sql
        SELECT 字段1[,字段2,字段3,...]
        FROM 表1[,表2,表3...];
        ```
        
        - SELECT子句中可以使用  " * "  表达查询所有的字段。
            - *工作中一般不会用星，就算全字段查也要全写上*
            - 因为带 " \* " 的话，SQL在执行时，数据库会先解析 " \* "，这个操作会导致数据库先查看内部数据字典了解表的字段后才能查看表中数据。由于查询是非常频繁的，因此每次查看数据字段无疑是浪费性能且消耗时间的操作！
              全字段查询时应当*在SELECT子句中将所有字段全部列出来*（*后期Java程序执行时*）。
              如果是*手动临时查询，可以使用 " \* "*。
            ```sql
            查询teacher表中的所有数据(Java程序执行时不推荐使用)
            SELECT * FROM teacher;
            
            查看每个老师的名字，职称，工资，性别
            SELECT name ,title,salary,gender
            FROM teacher;
            ```


#### **WHERE子句**

 - 在DQL中用于*筛选查询的记录*。最终数据库*仅将满足WHERE子句条件的记录体现在结果集中*。
    
    - 例：
        - 查看职称为"一级讲师" 的老师的名字和工资
        ```sql
        1：查询的是老师信息，能确定FROM中的表名为teacher。
        2：查看的是老师的名字和工资，能确定SELECT子句中的字段name和salary。
        3：由于仅查看"一级讲师"，能确定WHERE子句过滤条件为title = '一级讲师'
        SELECT name,salary,title
        FROM teacher
        WHERE title = '一级讲师';
        ```
        - 查看所有职位是 '大队长' 的同学的年龄和性别
            ```sql
            SELECT name,age,gender
            FROM student
            WHERE job = '大队长';
            ```


#### **使用 AND（与）和  OR（或）来连接多个条件进行查询**
- *AND*：都为真才为真。
- *OR*：都为假时才为假。
- 例：
    - 查看7岁的大队长都是谁，列出名字，年龄，性别，职位
       ```sql
       SELECT name,age,gender,job 
       FROM student
       WHERE age = 7 AND job = '大队长';
       ```
    - 查看所有一级讲师和三级讲师的名字，职称，工资
       ```sql
       SELECT name,title,salary
       FROM teacher
       WHERE title = '一级讲师' OR title = '三级讲师';
       ```
    - 查看班级编号在6(含)以下的所有大队长和中队长的名字、年龄、职位、班级编号
       ```sql
       Select name, age, job, class_id  
       From student  
       WHERE class_id <= 6  
       AND (job = '大队长' OR job = '中队长');
       ```


#### **IN（列表）：等于列表其中之一（在列表中）**
- 例：
    - 查看所有的大队长，中队长，小队长的名字，年龄，性别
         ```sql
         SELECT name,age,gender
         FROM student
         WHERE job IN('大队长','中队长','小队长');
         等价于：
         SELECT name,age,gender
         FROM student
         WHERE job = '大队长' OR job = '中队长' OR job = '小队长';
         ```

- **NOT IN（列表）：不在列表中，不能等于列表中的项**
    - 例：
        - 查看一级讲师和二级讲师以外的所有老师的名字，职称，工资
        ```sql
        SELECT name,title,salary
        FROM teacher
        WHERE title NOT IN('一级讲师','二级讲师');
        ```


#### **BETWEEN...AND... ：在一个范围之内(包含首尾)**
- **NOT BETWEEN...AND...：不在一个范围之内(包含首尾)**

- 查看工资在2000-5000之间的老师的名字，工资，职称
    ```sql
    SELECT name,salary,title
    FROM teacher
    WHERE salary BETWEEN 2000 AND 5000;
    等价于：
    SELECT name,salary,title
    FROM teacher
    WHERE salary >= 2000 AND salary <=5000;
    ```


#### **DISTINCT：去重操做**
- **在结果集 中去除 指定字段值相同的记录**
- *语法：*
    ```sql
    SELECT DISTINCT 字段名
    ```
- DISTINCT关键字 *必须紧跟在SELECT子句之后*。
- DISTINCT后面可以指定多个字段，这几个字段的组合中*相同的记录*会在结果集 中被去除。
- 例如：
    - 查看老师的职称都有哪些
    ```sql
    SELECT title
    FROM teacher;
    // 上述SQL语句的查询结果集是展现每个老师的职称，与查询需求不匹配
    
    SELECT DISTINCT title
    FROM teacher;
    ```
    - 查看各个年龄的职称有哪些
        ```sql
        SELECT DISTINCT age,title
        FROM teacher;
        ```


#### **模糊查询（LIKE）**
- **LIKE中有两个通配符：**
    -  *_* ：下划线表示一个字符（*1个*）
    -  *%* ：百分号表示任意个字符（*0 ~ 多个*）
    - **组合：**
        - *%x%*：字符串中*含有* 'x'。
        - *x%*：字符串以 'x' *开头*。
        - *%x*：字符串以 'x' *结尾*。
        - *\_\_x (两个下划线)*：字符串为*三个字符*，且*最后一个*字符为 'x'。
        - *\_x\_* ：字符串为*三个字符*，且*第二个*字符为 'x'。
        - *\_x%*：字符串第*二个字符*为 'x'。
        - *%x%y*：字符串含有 *'x' 和 'y'*。 
    - 例：
        - 查看名字中带有 '苍' 的老师
        ```sql
        SELECT name, title, salary
        FROM teacher
        WHERE name LIKE '%苍%';     // 字符串中含有'苍'字。
        ```
        - 查看姓 '张' 的学生都有谁
        ```sql
        SELECT name, age, job, gender
        FROM student
        WHERE name LIKE '张%';     // 字符串由'张'开头。
        ```
        - 查看名字是以 '晶' 结尾的老师都有谁
        ```sql
        SELECT name, title, salary
        FROM teacher
        WHERE name LIKE '%晶';     // 字符串以'晶'结尾。
        ```
        - 查看 三个字名字 且 最后一个字 是 '平' 的学生都有谁
        ```sql
        SELECT name, age, job, gender
        FROM student
        WHERE name LIKE '_ _平';     // 字符串 为三个字符 且 最后一个字为'平'
        // '_ _平'，下划线中间的空格是不需要添加的，只是为了看清楚
        ```
        - 查看 三个字名字 中 第二个字符是 '平' 的学生都有谁
        ```sql
        SELECT name, age, gender, job
        FROM student
        WHERE name LIKE '_平_';     // 字符串为 两个字符 且 第二个字是 '平'
        ```
        - 查看职位是'什么课什么表'的学生都有谁
        ```sql
        SELECT name,age,gender, job
        FROM student
        WHERE job LIKE '%课%表';    // 字符串含有 什么课什么表
        ```


#### **NULL值判断**
- 等于NULL：*IS  NULL*。
- 不等于NULL：*IS  NOT  NULL*。等于0也不为NULL。
- *不能直接用 == 或 <> 判断NULL值！* 
- 例：
    - 查看没有奖金的老师都有谁（奖金字段值为NULL的记录）
    ```sql
    SELECT name,salary,comm
    FROM teacher
    WHERE comm == NULL;
    // 在数据库中，上述不能判断 comm 为 NULL
    
    SELECT name,salary,comm
    FROM teacher
    WHERE comm IS NULL;
    ```
    - 查看有奖金的老师都有谁
    ```sql
    SELECT name,salary,comm
    FROM teacher
    WHERE comm IS NOT NULL;
    ```


#### **ORDER BY 子句（排序）**
- ORDER BY 子句*用于指定 查询的结果集 按照 指定的字段进行排序*。
- *语法：*
    - *ORDER  BY  字段名  \[ASC\]*：将结果集按照指定的字段值  *升序排序*（从小到大）。
    - *ORDER  BY  字段名  DESC*：将结果集按照指定的字段值  *降序排序*（从大到小）。
    - *ORDER  BY  字段1  \[ASC\]/DESC, 字段2  \[ASC\]/DESC...*：ORDER  BY  可以按照多字段排序：
        - 多字段排序时，首先结果集按照第一个字段，
        - 当第一个字段值有相同的记录再按照第二个字段的排序方式继续排序，
        - 以此类推
    - ORDER BY 不指定排序方式时，*默认为升序*。
    - ORDER子句只能是*DQL语句中最后一个子句*。（全都查到了再排序）
    - *日期*是*越早的越小*，*越晚的越大*。
    - *相同的值*还按照*原本在字段中的顺序*。
- 例：
    - 查看老师工资的排名情况，从大到小
    ```sql
    SELECT name,title,salary
    FROM teacher
    ORDER BY salary DESC;
    ```
    - 查看老师的奖金情况，从小到大
    ```sql
    SELECT name,title,comm
    FROM teacher
    ORDER BY salary;
    ```
    - 查看学生的生日从远到近
    ```sql
    SELECT name,age,birth
    FROM student
    ORDER BY birth;
    ```
    - 查看7~10岁学生 名字，年龄，生日，查看时按照年龄从小到大(生日日期从大到小)。
    ```sql
    SELECT name,age,birth
    FROM student
    WHERE age BETWEEN 7 AND 10
    ORDER BY age,birth DESC;     // age,birth两个排序是 各自排序
    // age默认从小到大，birth从大到小排序
    ```
    - 查看老师的工资和奖金，按照工资的降序，奖金的升序
    ```sql
    SELECT name,title,salary,comm
    FROM teacher
    ORDER BY salary DESC,comm;
    // 第一个字段没有重复的，第二个字段就没有意义了
    ```


#### **分页（LIMIT）**
- 当一条DQL语句可以查询出很多行记录时，为了减少资源开销，以及网络间的传送速度，我们通常都会使用分页查询来分批，分段地将DQL查询的结果集获取到。
- *分页是方言*。
    - 在*Mysq*中：分页使用*LIMIT*实现。
    - 在Oracle中：使用ROWNUM实现。
- *语法：*
    - 在*ORDER  BY子句中*使用 LIMIT。
        - N表示：*跳过 N个 结果集的记录*。
        - M表示：从*跳过N条记录后*  开始 *连续显示多少条记录*。
        - *分页上有两个条件：* 1、页数，2、每页显示的条数
            - N：（页数 - 1）* 每条的页数
            - M：每页显示的条数
            - 例如：
    ```sql
    ORDER BY 字段
    LIMIT N,M;
    ```
- 例：
    - 查看老师工资前五名
    ```sql
    SELECT name,salary
    FROM teacher
    ORDER BY salary DESC 
    LIMIT 0,5;
    ```
    - 按照老师奖金的降序排序后，每页显示3条，显示第五页
    ```sql
    SELECT name,comm
    FROM teacher
    ORDER BY comm DESC
    LIMIT 12,3;       // 12：3*(5-1)
    ```


#### **DQL中可以使用函数或表达式进行查询**

- **在SELECT子句中使用函数或表达式：**
    - *使用表达式：*
        - 例：
            - 查看每个老师的年薪是多少
            ```sql
            SELECT name,salary,salary*12
            FROM teacher;
            ```
    - *使用函数：*
        - 在计算表达式中，任意数字与NULL运算结果都是NULL
            - *IFNULL(arg1,arg2)函数*
                - 当arg1*不为NULL*时，函数*返回 arg1的值*；
                - 当arg1*为NULL*时，函数*返回 arg2的值*。
                - IFNULL作用时*旨在*：*将一个NULL值换成一个非NULL值*。
                - 内部逻辑：
                    ```sql
                    IFNULL(arg1,arg2){
                        if(arg1 != NULL){
	                        return arg1;
                        }else{
	                        return arg2;
                        }
                    }
                    ```
        - 例：
            - 查看每个老师的工资加奖金是多少
            ```sql
            SELECT name,salary,comm,salary+comm
            FROM teacher;
            // 上述SQL对于salary+comm而言，若comm字段为NULL时计算结果也为NULL
            
            所以使用 IFNULL函数进行判断再计算
            SELECT name,salary,comm,IFNULL(comm,0),salary+IFNULl(comm,0)
            FROM teacher;
            上述SQL会将comm字段为NULL的记录改为0
            ```

- **在WHERE子句中使用函数或表达式作为过滤条件**
    - *使用表达式：*
        - 例：
            - 查看年薪小于6万的老师都有谁
            ```sq
            SELECT name,salary,salary*12
            FROM teacher
            WHERE salary*12 < 60000;
            ```

        - *使用函数：*
            - **比较运算时也会忽略NULL值**
                - *也使用IFNULL(arg1,arg2)函数*
            - 查看奖金小于3000的老师都有谁
            ```sql
            SELECT name,comm
            FROM teacher
            WHERE comm < 3000;      
            // 上述会忽略奖金为NULL的记录
            
            SELECT name,comm
            FROM teacher
            WHERE IFNULL(comm,0) < 3000; 
            ```


#### **别名**
- 别名可以在*SELECT子句*中 *为函数、表达式起别名*，使得结果集 中该字段*可读性更强*。
- 别名*也可以用在其他子句*上，比如在*FROM子句*中*为表取别名*，*便于关联查询*。
- 当*SELECT子句中含有函数或表达式*时，通常都会*指定别名*。
- 别名是 *中文* 时，最好*加引号*。
- *语法：*
    - *字段名（空格）别名*
        ```sql
        SELECT salary*12 年薪
        ```
    - *字段名  AS  别名*
        ```sql
        SELECT salary*12 AS 年薪
        ```
    - *字段名  [AS]  '字段名'*
        ```sql
        SELECT salary*12 AS '年薪'
        SELCET salary*12 '年薪'
        ```
    - *字段名  [AS]  "字段名"*
        ```sql
        SELECT salary*12 AS "年薪"
        SELECT salary*12 "年薪"
        ```
    - *当别名中含有  SQL关键字  或  空格  时要加引号*
        ```sql
            // 1.含有关键字
            SELECT name,salary*12 from    
            FROM teacher;
            // 由于from是关键字，SQL语句语法错误(两次FROM)
            正确写法
            SELECT name,salary*12 'from'
            FROM teacher;
            
            // 2.含有空格
            SELECT name,salary*12 年 薪
            FROM teacher;
            // 数据库理解 年 为别名，再空格认为 薪 语法错误
            正确写法
            SELECT name,salary*12 '年 薪'
            FROM teacher;
            ```

- 例：
    - 查看每个老师的年薪是多少
    ```sql
    SELECT name,salary, salary*12 年薪        字段名（空格）别名
    FROM teacher;
    ```


#### **聚合函数**
- 聚合函数（也成为多行函数、分组函数）是用来*对结果集按照指定字段 统计* 的。
- 聚合函数可以将多行记录中的指定字段*统计出 一条 结果*。
- 聚合函数 *不能放在WHERE子句中*。
- 聚合函数：
    - *MIN*：统计结果集中指定字段值*最小的*。
    - *MAX*：统计结果集中指定字段*最大的*。
    - *AVG*：统计结果集中指定字段的*平均值*。
    - *SUM*：统计结果集中指定字段的*总和*。
    - *COUNT*：统计结果指定字段*非空值共多少行*。（忽略NULL值）
        - *统计共有多少行* 时一般使用 *COUNT( \* )*，而不是不指定某个字段。
        - *COUNT( \* )* 作为统计*被所有数据库都进行了优化*，因此*统计记录数时都用它*。
    
    - *MIN、MAX、AVG、SUM*是对*字段值*的统计。*COUNT*是对*记录个数*的统计。
    
- 使用聚合函数时，通常要*先将参与统计的记录 查询出来*后*再决定添加何种聚合函数*进行统计。
- 例：
    - 查看所有老师的平均工资是多少
    ```sql
    1:准备参与统计的记录对应的SQL语句
    下述SQL用来查询出所有老师的工资：
    SELECT salary
    FROM teacher;
    
    2:为salary字段添加聚合函数，求所有记录salary的平均值
    SELECT AVG(salary)
    FROM teacher;
    ```
    - 查看所有老师的最高工资、最低工资、工资总和、平均工资
    ```sql
    SELECT MAX(salary),MIN(salary),SUM(salary),AVG(salary)
    FROM teacher;
    ```
    - 查看负责课程编号1的老师的平均工资是多少
    ```sql
    1：查询出负责课程编号1的老师的工资分别是多少
    SELECT salary
    FROM teacher
    WHERE subject_id = 1;
    
    2：在上述SQL中对salary字段添加聚合函数求平均值
    SELECT AVG(salary)
    FROM teacher
    WHERE subject_id = 1;
    ```
    - 查看共有多少位老师
    ```sql
    SELECT name
    FROM teacher; 
    
    SELECT COUNT(name)
    FROM teacher;
    上述SQL统计结果共20条。因为teacher表中每条记录的name都有值
    
    SElECT COUNT(comm)
    FROM teacher;
    上述SQL统计结果共17条。原因：teacher表中有3条记录comm字段值为NULL
    聚合函数是忽略NULL值的
    
    // *统计共有多少行 时一般使用 COUNT(*)，而不是不指定某个字段。
    // COUNT(*) 作为统计 被所有数据库都进行了优化，因此 统计记录数时都用它。
    SELECT COUNT(*)
    FROM teacher;
    ```


#### **分组（GROUP  BY 子句）**

GROUP BY子句在DQL语句中是*对查询结果集*按照*指定的字段 值相同的记录 进行分组*，*配合聚合函数*进行组内统计。

**若DQL的SELECT子句中没有聚合函数时，不会使用GROUP BY子句。**

**在SELECT子句中 且 不在聚合函数中的字段：都必须出现在GROUP BY子句中**

- **按照单字段分组**
    - 例：
        - 教每门课程的老师的平均工资是多少?
            ```sql
            SELECT AVG(salary),subject_id  
            FROM teacher  
            GROUP BY subject_id  
              
            GROUP BY子句的作用是将:  
            SELECT salary,subject_id  
            FROM teacher  
            查询结果集 按照subject_id字段值相同的记录分组，在组内统计结果。
            ```

        ![[分组.png]]

        - 查看每个班级各多少学生?(将student表中所有记录按照class_id字段值相同的记录分组，组内统计记录数)
            ```sql
            SELECT COUNG(*),class_id  
            FROM student  
            GROUP BY class_id
            ```
        - 查看学校每种职位的学生各多少人?以及最大生日和最小生日
            ```sql
            SELECT COUNT(*) '人数',MIN(birth) '最小生日',MAX(birth) '最大生日',job  
            FROM student  
            GROUP BY job
            ```

- **按照多字段分组**
    - GROUP  BY子句若*指定了多个字段*，那么只有当这几个字段的记录都相同才会被划分为一组
    - 例：
        - 查看每个班个中各职位有多少人
            ```sql
            SELECT class_id,job,COUNT(*)
            FROM student
            GROUP BY class_id,job;
            ```

- **在分组统计时添加过滤条件**
    - **HAVING子句**
        - HAVING子句*仅跟在GROUP BY子句之后*，因此若没有GROUP BY子句就不会写HAVING子句。
        - HAVING子句也是用于*添加过滤条件*的。
        - *过滤时机*：分组统计处结果后进行二次过滤的。
        
        - **HAVING子句与WHERE子句的区别：**
            - **过滤的时机不同：WHERE先执行，用于确定查询出的结果集，HAVING后执行，用于确定保留结果集中的哪些分组。**
            - *HAVING子句是对分组过滤的*：只有*满足HAVING子句条件的分组*才会进行SELECT中相应的查询。
            - *WHERE子句是对记录过滤的*：只有*满足WHERE子句条件的记录*才会进行SELECT中相应的查询。
        
        - 例：
            - 仅查看平均工资高于6000的那些课程的老师平均工资
                ```sql                                                                                                            子句执行顺序：
                SELECT AVG(salary) avg,subject_id   4：最后查询对应的内容
                FROM teacher                        1：确定数据来自哪里
                GROUP BY subject_id                 2：确定数据来自哪个分组
                HAVING avg > 6000;                  3：确定仅保留哪些分组
                ```
            - 查看每个科目老师的的平均工资，前提是该科老师最高工资高于9000
                ```sql
                SELECT subject_id,AVG(salary)
                FROM teacher
                GROUP BY subject_id
                HAVING MAX(salary) > 9000;
                ```
            - 查看各科目老师的工资总和是多少？前提是该科老师的平均奖金要高于4000
                ```sql
                SELECT subject_id,SUM(salary)
                FROM teacher
                GROUP BY subject_id
                HAVING AVG(IFNULL(comm,0)) > 4000
                ```
    
    - *聚合函数不能被应用在WHERE子句。*
        - 原因：*过滤时机不对*
            - WHERE是在 *查询表中数据时*  过滤。
            - 聚合函数是在 *查询表中数据后* 才统计的。
            - 聚合函数像作为过滤条件（将统计结果作为过滤条件），统计的前提是要先查询出结果集，而查询结果集是WHERE的时机，因此根据统计结果在进行过滤应当已经是WHERE之后进行的了。
    - 例：
        - 仅查看平均工资高于6000的那些课程的老师平均工资
            ```sql
            SELECT AVG(salary) avg,subject_id
            FROM teacher
            WHERE avg > 6000     // 会报错
            GROUP BY subject_id;
            ```

- **按照聚合函数排序**
    - 在SELECT子句中为聚合函数取别名，这样方便在ORDER BY子句中排序
    - 例：
        - 查看每个科目老师的平均工资，并按照平均工资从少到多排序
            ```sql
            SELECT subject_id, AVG(salary)
            FROM teacher
            GROUP BY subject_id
            ORDER BY AVG(salary);
            
            // 建议为聚合函数取别名，并按照该别名排序
            SELECT subject_id, AVG(salary) avg
            FROM teacher
            GROUP BY subject_id
            ORDER BY avg;
            ```


#### **子查询**

**嵌套在其他SQL语句中的一条DQL语句就被称为子查询**

- **子查询使用场景：**
    - DQL中：可以基于一个查询结果集再进行*查询*（先执行这条DQL就是子查询，也是子查询最常用的场景）。
    - DML中：基于一个查询结果集进行*增删改操作*。
    - DDL中：基于一个查询结果集进行*数据库对象的操作*（*创建表，创建视图*）。

- **子查询分类：**
    - *单行单列* 子查询：查询结果集中只有一个值。
    - *多行单列* 子查询：查询结果集中有多个值。
    - *多行多列* 子查询：拆线呢结果集就是一张表。

- **在DQL语句中使用子查询（多为单行单列子查询）**
    - *在DQL中子查询部分要被 "()" 包起来。*
    - 例：
        - 查看那个老师的工资高于王克晶的工资？
            1. 查看王克晶的工资
                ```sql
                SELECT name,salary
                FROM teacher
                WHERE name = '王克晶';
                ```
            2. 查看谁的工资高于8000（王克晶工资）
                ```sql
                SELECT name,salary
                FROM teacher
                WHERE salary > 8000;
                ```
            3. 将步骤1 SQL替换为步骤2的8000
                ```sql
                示意：
                不是真实语法！！！
                int money = SELECT salary FROM teacher WHERE name = '王克晶';
                查询示意：
                SELECT name,salary
                FROM teacher
                WHERE salary > money;
                
                真实写法：
                SELECT name,salary
                FROM teacher
                WHERE salary > (SELECT salary 
                                FROM teacher 
                                WHERE name = '王克晶');
                ```
            
            - 查看那些高于老师平均工资的老师工资都是多少
                ```sql
                SELECT name,salary
                FROM teacher
                WHERE salary > (SELECT AVG(salary)
                                FROM teacher);
                ```
            - 查看和 '李费水' 在同一个班的学生都有谁
                ```sql
                SELECT name
                FROM student
                WHERE class_id = (SELECT class_id    // 先获取'李费水' 在哪个班
                                  FROM student
                                  WHERE name = '李费水');
                ```
            - 查看工资最高的老师的工资和奖金是多少
                ```sql
                SELECT name,salary,comm
                FROM teacher
                WHERE salary = (SELECT MAX(salary)
                                FROM teacher);
                ```
    
    - 在*单行单列子查询中*，若我们使用其*作为过滤条件使用*时，可以搭配 *=，>，>=，<，<=，<>* 使用。
    - 在*多行单列子查询中*（*查询结果集有多个值*），在*过滤条件*中要搭配：*IN、ANY、ALL*。
        - *ANY* 与 *ALL* 是与：>, >=, <, <= *联合使用* 的。
            - *> ANY(列表)*：大于 列表*其中之一*(大于最小的即可)。
            - *< ANY(列表)*：小于 列表*其中之一*(小于最大的即可)。
            - *> ALL(列表)*：大于 列表*所有*(大于最大的)。
            - *< ALL(列表)*：小于 列表*所有*(小于最小的)。
        - *IN(列表)* 或 *NOT IN(列表)*：*在列表中* 或 *不在列表中*。
        - 上述所说的列表指的是*一个 多行单列 子查询的结果集*。
        - 例：
            - 查看与"祝雷"和"李费水"在同一个班的学生都有谁?
                ```sql
                1:查看祝雷和李费水的班级号?  
                SELECT class_id  
                FROM student  
                WHERE name IN ('祝雷','李费水')  
                  
                2:查看与"祝雷"和"李费水"在同一个班的学生都有谁?  
                SELECT name,class_id  
                FROM student  
                WHERE class_id = (SELECT class_id  
                                FROM student
                                WHERE name IN('祝雷','李费水'));
                上述SQL执行后会报错，因为子查询返回了多个值，"="不能同时等于多个值
                
                正确写法：
                // 使用 IN
                SELECT name,class_id  
                FROM student  
                WHERE class_id IN (SELECT class_id  
                                  FROM student                  
                                  WHERE name IN('祝雷','李费水'));
                ```
            - 查看比教科目2和科目4老师工资都高的老师都有谁?
                ```sql
                1:查看科目2和科目4老师的工资都是多少?  
                SELECT salary FROM teacher WHERE subject_id IN(2,4)  
                  
                2:查看比教科目2和科目4老师工资都高的老师都有谁?
                SELECT name,salary,subject_id  
                FROM teacher  
                WHERE salary > ALL(SELECT salary   
                                 FROM teacher   
                                 WHERE subject_id IN(2,4))
                ```

- **在DML中使用子查询**
    - 例：
        - 将负责与"范传奇"教同一科目的所有老师工资涨500
            ```sql
            1:查看范传奇负责的科目号？  
            SELECT subject_id  
            FROM teacher  
            WHERE name='范传奇';
              
            2:更新数据  
            UPDATE teacher  
            SET salary = salary + 500  
            WHERE subject_id = (SELECT subject_id
                                FROM teacher
                                WHERE name='范传奇');
            ```


- **在DDL中使用子查询（多为多行多列子查询）**
    - 在创建表时，可以将一个*子查询的结果集当作表* 创建出来，后期使用的视图也是如此。
    - **AS** 后直接 接 子查询。
    - 如果子查询的*SELECT子句中的字段是函数或表达式*，则必须要*指定别名*（会作为新创建的表的字段名）
    - 例：
        - 创建一张表用于记录老师工资的统计情况（按照科目）。要记录每门课老师的最高工资、最低工资、平均工资和工资总和以及科目编号。表名：teacher_salary_info
            ```sql
            1:创建表
            CREATE TABLE teacher_salary_info(
	            max_salary INT,
	            min_salary INT,
	            avg_salary INT,
	            sum_salary INT,
	            subject_id INT
            );
            2:先执行DQL查询对应结果，再使用DML中的INSERT语句逐行插入。
              若数据太多，工作量太大且笨重
              SELECT MAX(salary),MIN(salary),AVG(salary),SUM(salary),subject_id
              FROM teacher
              GROUP BY subject_id;
              
            实际写法：
            注意：如果子查询的SELECT子句中的字段是函数或表达式，
                  则必须要指定别名（会作为新创建的表的字段名）
            CREATE TABLE teacher_salary_info
            AS
            SELECT MAX(salary) max_salary, MIN(salary) min_salary,
                   AVG(salary) avg_salary, SUM(salary) sum_salary,
                   subject_id
            FROM teacher
            GROUP BY subject_id;
            ```


#### ***关联查询（重点！！！）***

**结果集中的字段来自于多张表，需要联合多张表进行查询**

表与表之间的数据存在着一种对应关系，这种对应关系被称为表与表之间的关联关系

- **表之间的关联关系分为三种：**
    - *一对一*：A表的一条记录 仅对应 B表的一条记录。反之亦然。
    - *一对多*：A表的一条记录 对应 B表的多条记录，反之B表的一条记录 仅对应 A表的一条记录。
    - *多对多*：两张表的一条记录 都对应 另一张表的多条记录。

- **在关联查询中必须要指定连接条件**，
    - **连接条件作用**是：让数据库知道A表的记录与B表的哪些记录对应。
    - 关联查询中**不指定连接条件**会**产生笛卡尔积**，**笛卡尔积通常是一个无意义的结果集**（少数特殊业务除外）。
  
- **多张表要进行关联查询时**，要满足当 *N张表* 联合查询时要指定 *最少N-1个连接条件*。
    - 例如：3张表关联查询至少要有2个连接条件。

- **笛卡尔积**
    - *缺少连接条件*产生笛卡尔积。
    - 特点：
        - 以两张表A和B关联查询，结果集中包含的数据A表每条记录与B表每条记录产生的记录数。
        - 记录总数应当是：A表记录数 * B表记录数。

- **关联查询语法：**
    ```sql
    SELECT 字段...
    FROM 表A,表B,...
    WHERE 连接条件
    AND 过滤条件
    注：如果在关联查询中含有过滤条件，则必须与连接条件同时满足，否则会产生笛卡尔积。
    ```
  
    - 例：
        - 查看老师的名字和其任课的科目名称
            ```sql
            SELECT t.name,s.name
            FROM teacher t, subject s
            WHERE t.subject_id = s.id;
            
            实际查询时，数据库会从teacher表中将其中一条记录与subject表中的
            每条记录都关联一次，
            但是,仅将 符合连接条件的记录 分别取出 查询的字段 构成结果集中的一条记录。
            ```
            ![[关联查询1：连接条件.png]]
        - 当*不指定连接条件*时，teacher表会和subject表里*每条记录都关联一次并在结果集产生一条记录（产生了笛卡尔积）* 。
            ![[关联查询2：笛卡尔积.png]]

        -  查看班级的名称和对应的班主任（老师）是谁
            ```sql
            1:数据需要来自哪些表
            班级名称来自class表，班主任名字来自teacher表
            FROM class c, teacher t
            
            2：明确class表与teacher表记录之间的对应关系（关联关系）
            class表记录中teacher_id字段的值与teacher表中的id字段的值
            相同的记录产生对应关系
            明确WHERE子句连接条件 WHERE c.teacher_id = t.id
            
            SELECT c.name, t.name
            FROM class c, teacher t
            WHERE c.teacher_id = t.id;
            ```

        - 查看每个学生的名字，年龄，以及其所在班级名称和所在楼层
            ```sql
            SELECT s.name,s.age,c.name,c.floor
            FROM student s, class c
            WHERE s.class_id = c.id;
            ```

- **在关联查询中可以指定过滤条件**
    - *过滤条件要与连接条件同时满足*
    - 例：
        - '王克晶' 是哪个班的班主任？查看班级名称，所在楼层和班主任名字及工资
            ```sql
            SELECT c.name,c.floor,t.name,t.salary
            FROM teacher t, class c
            WHERE t.id = c.teacher_id;   // 到这里只会查询到所有老师的信息
            AND t.name = '王克晶';       // 只查看符合条件的所有老师中的 '王克晶'
            ```
        - 查看3年级的班级班主任都是谁？列出班级名称、所在楼层，班主任名字和工资
            ```sql
            SELECT c.name,c.floor,t.name,t.salary
            FROM teacher t, class c
            WHERE t.id = c.teacher_id
            AND c.name LIKE '3年级%'
            ```
        - 查看5年级的中队长都有谁？列出学生名字、年龄、性别、职位，班级的名字以及楼层
            ```sql
            SELECT s.name,s.age,s.gender,s.job,c.name,c.floor
            FROM student s, class c
            WHERE s.class_id = c.id     连接条件
            AND c.name LIKE '5年级%'    过滤条件
            AND s.job = '中队长';       过滤条件
            ```

- **N张表关联至少要有N-1条连接条件**
    - 例：
        - 查看 "范传奇" 所带班级的学生都有谁？学生的名字、年龄，班级名称，老师名字
            ```sql
            1:查询的数据来自哪些表
            学生信息来自student表，班级信息来自class表，老师信息来自teacher表
            FROM student s, class c, teacher t
            
            2:明确连接条件
              3张表至少要有2个连接条件
            学生表与班级表中的记录对应关系
            s.class_id = c.id
            班级表与老师表中的记录对应关系
            c.teacher_id = t.id
            
            3:过滤条件来自哪些表
            老师名字叫'范传奇'，来自teacher表
            
            SELECT s.name,s.age,c.class,t.name
            FROM student s, class c, teacher t
            WHERE s.class_id = c.id     连接条件
            AND c.teacher_id = t.id     连接条件    注：所有的连接条件要同时满足
            AND t.name = '范传奇';     过滤条件
            ```
            ![[关联查询3：N张表.png]]
        - 查看1年级1班的同学的名字和来自的城市
            ```sql
            1:查询的数据来自哪些表
            班级信息：class表，学生信息：student表，城市信息：location表
            FROM class c, student s, location l
            
            2:明确连接条件
              3张表至少2个连接条件
            班级表与学生表中的记录对应关系
            c.id = s.class_id
            学生表与城市表中的记录对应关系
            s.location_id = l.id
            
            3:过滤条件来自哪些表
            班级为1年级1班
            c.name = '1年级1班'
            
            SELECT s.name,c.name,l.name
            FROM class c, student s, location l
            WHERE c.id = s.class_id     // 连接条件
            AND s.location_id = l.id    // 连接条件
            AND c.name = '1年级1班';   // 过滤条件
            ```
        - 全校最小的同学的班主任是谁?
            ```sql
            SELECT s.name,s.birth, t.name  
            FROM student s, teacher t, class c  
            WHERE s.class_id = c.id  
            AND c.teacher_id = t.id  
            AND s.birth = (SELECT MAX(birth)  
                           FROM student);
            ```
        - 王克晶所带的班级学生都来自哪些城市(去重)?
            ```sql
            SELECT DISTINCT l.name  
            FROM teacher t, class c, student s, location l  
            WHERE t.id = c.teacher_id  
            AND c.id = s.class_id  
            AND s.location_id = l.id  
            AND t.name = '王克晶';
            ```
        - 所在4楼的班里的大队长和中队长以及班主任都是谁?
            ```sql
            SELECT s.name,s.job, t.name,c.name  
            FROM student s, teacher t, class c  
            WHERE s.class_id = c.id  
            AND c.teacher_id = t.id  
            AND c.floor = 4  
            AND s.job IN ('大队长','中队长');
            ```

- **关联查询中使用聚合函数**
    - 例：
        - 查看 '范传奇' 所带班级共多少人
            ```sql
            1:要参与统计的记录都查出来
            SELECT t.name, s.name, c.name
            FROM teacher t, class c, student s
            WHERE t.id = c.teacher_id
            AND c.id = s.class_id
            AND t.name = '范传奇';
            
            2:在上述SQL中应用聚合函数
            SELECT COUNT(*)
            FROM teacher t, class c, student s
            WHERE t.id = c.teacher_id
            AND c.id = s.class_id
            AND t.name = '范传奇';
            ```
        - 查看教语文的老师平均工资是多少?
            ```sql
            1.准备参与统计的记录(教语文的老师的工资信息)  
            SELECT s.name,t.name,t.salary  
            FROM subject s,teacher t  
            WHERE s.id=t.subject_id  
            AND s.name='语文';
              
            2:对工资字段加聚合函数AVG  
            SELECT AVG(t.salary)  
            FROM subject s,teacher t  
            WHERE s.id=t.subject_id  
            AND s.name='语文';
            ```
        - 查看每门课的平均工资是多少?(GROUP BY)
            ```sql
            1:准备参数统计的记录(科目信息，老师名字，老师工资)
            SELECT s.name, t.name,t.salary
            FROM subject s, teacher t
            WHERE s.id = t.subject_id;
            
            2:对工资求平均值，要按照科目名称相同的老师记录分组
            SELECT s.name, AVG(t.salary)
            FROM subject s, teacher t
            WHERE s.id = t.subject_id
            GROUP BY s.name;
            ```
        - 仅查看平均工资高于6000的那些科目的老师平均工资是多少,并从多到少排序
            ```sql
            SELECT s.name, AVG(t.salary) avg
            FROM subject s, teacher t
            WHERE s.id = t.subject_id
            GROUP BY s.name
            HAVING avg > 6000
            ORDER BY avg DESC;
            ```
        - 查看工资最低的老师班里的学生共多少人
            ```sql
            1：未知条件 ->最低工资是多少
            SELECT MIN(salary)
            FROM teacher
            
            2：准备参与统计的数据
               工资最低的那个老师班里的学生都有谁
               SELECT s.name, c.name, t.name 
               FROM teacher t, class c, student s
               WHERE t.id = c.teacher_id
               AND c.id = s.class_id
               AND t.salary = (SELECT MIN(salary)
                               FROM teacher);
               
            3：添加聚合函数统计
            SELECT COUNT(*)
            FROM teacher t, class c, student s
            WHERE t.id = c.teacher_id
            AND c.id = s.class_id
            AND t.salary = (SELECT MIN(salary)
                            FROM teacher);
            ```

- **主键与外键**
    - **在多表关联查询中，连接条件通常就是参与查询的表中的某字段的等值连接。**
    - **通常就是主键与外键的等值连接。**
    - 例：
        - 以student表与class表关联查询为例
        - 班级表中id字段作为班级表的*主键* (primary Key)，特点：*非空且唯一*
        - 学生表中*class_id字段*，该字段记录着该学生所在的班级的信息。*为了让学生信息与其所在的班级信息产生对应关系*，而设计了class_id字段，用于记录其所在的班级在班级表(class表)中记录的该班的id值，此时在学生表中的class_id字段就被称为是其对应的class表主键所关联的*外键*。
    - 在关联关系中，*存在外键字段的表* 与之对应的 *保存主键字段值的表* 就是 *“多对一”* 的关系，保存外键字段的表在关联关系中处于 “多” 的一方。
    - **外键在数据库中也存在外键约束**
        - 外键约束的*要求：*
            - *外键字段保存的值* 必须是 其对应的主键字段中*已有的值*。
            - 例如：student表与class表。student表的class_id为外键，对应的class表id这个主键
                - class表有3条记录，id分别是1,2,3。此时student表任何记录中class_id可选值仅为1,2,3,NULL，若class_id值为4则违反外键约束。
            
            - *删除主键表中的某条记录时*，必须要保证该记录主键*所对应的外键中没有一条记录的外键保存该主键字段值*。
                - 例如：student表与class表。student表的class_id为外键，对应的class表id这个主键
                    - class表有1条id为1的班级记录，同时student表中有三个学生记录的class_id为1.此时若删除class表中id为1的这条记录是不被允许的(原因是3个学生的外键记录着该该主键值)。

- **多对多关系**
    - 当两张表双向都是一对多时，就是多对多关系
    
    - **关联关系表**
        - 多对多关系在表设计时，通常要添加一张 *关联关系表* 来维持两张表的多对多关系。
        - 关联关系表通常*不需要主键字段*，并且 *一定有两个字段* 分别是 *多对多关系的两张表中* 各自*主键所对应的外键字段*。
        - 例：
            - 学生表student与科目表subject就是多对多的。
            - 从学生表的角度触发：一个学生可以对应多个学生。
            - 从科目表角度出发：一个科目可以对应多个学生。
            - *关联关系表中有两个字段分别记录了这两张表的主键字段*。
            - t_stu_subject_score表，该表有两个字段*stu_id*，*subject_id*分别记录了*student表的id*和*subject表的id*。因此依靠这张表就可以实现student与subject表的多对多关系。
        
        - **实际查询就是一个三张表的关联查询**
        - 例：
            - 查看 '李费水' 的各科成绩
                ```sql
                SELECT s.name, su.name, sss.score  
                FROM student s, t_stu_subject_score sss, subject su  
                WHERE s.id = sss.stu_id  
                AND su.id = sss.subject_id  
                AND s.name = '李费水';
                ```
            - 查看1年级1班所有同学的语文成绩是多少?
                ```sql
                SELECT c.name, s.name, su.name, sss.score
                FROM class c, student s, subject su, t_stu_subject_score sss
                WHERE c.id = s.class_id
                AND s.id = sss.stu_id
                AND su.id = sss.subject_id
                AND c.name = '1年级1班'
                AND su.name = '语文';
                ```
            - 统计1年级1班数学成绩的平均值
                ```sql
                SELECT c.name, su.name, AVG(sss.score)
                FROM class c, student s, subject su, t_stu_subject_score sss
                WHERE c.id = s.class_id
                AND s.id = sss.stu_id
                AND su.id = sss.subject_id
                AND c.name = '1年级1班'
                AND su.name = '数学';
                ```
            - 查看 '刘苍松' 所带班级的学生的英语成绩
                ```sql
                SELECT t.name, c.name, s.name, su.name, sss.score
                FROM teacher t, class c, student s, t_stu_subject_score sss,
                     subject su
                WHERE t.id = c.teacher_id
                AND c.id = s.class_id
                AND s.id = sss.stu_id
                AND su.id = sss.subject_id
                AND t.name = '刘苍松'
                AND su.name = '英语';
                ```
            - 查看工资最高的老师所带班级的各科成绩的平均分、最高分和最低分
                ```sql
                SELECT t.name, t.salary, su.name, 
                AVG(sss.score), MAX(sss.score), MIN(sss.score)
                FROM teacher t, class c, student s, t_stu_subject_score sss,
                     subject su
                WHERE t.id = c.teacher_id
                AND c.id = s.class_id
                AND s.id = sss.stu_id
                AND su.id = sss.subject_id
                AND t.salary = (SELECT MAX(salary)
                                FROM teacher)
                GROUP BY su.name;
                ```


#### **内连接（JOIN...ON...）**

*语法：*
```sql
SELECT 子句
FROM 表1
JOIN 表2 ON 连接条件
[JOIN 表3 ON 连接条件 
 ...]
WHERE 过滤条件; 
```

- *内连接的查询与关联查询一致*，*区别在于：* 连接条件 *不是* 和过滤条件混在一起定义为WHERE子句中了，而是在每连接一张表是在ON子句中指定连接条件。
- *优点：* 连接条件与过滤条件是分开在不同子句定义的。结构看起来更清晰。
    WHERE子句中仅写过滤条件，而连接条件全部放在ON子句中。

- 例：
    - 查看1年级1班的所有同学的名字，年龄，性别
        ```sql
        关联查询写法:
        SELECT s.name, s.age, s.gender
        FROM student s, class c
        WHERE c.id = s.class_id
        AND c.name = '1年级1班';
        
        内连接写法：
        SELECT s.name, s.age, s.gender
        FROM student s
        JOIN class c ON s.class_id = c.id
        WHERE c.name = '1年级1班';
        ```
    - 查看教英语的老师都有谁
        ```sql
        SELECT t.name
        FROM teacher t
        JOIN subject su ON t.subject_id = su.id
        WHERE su.name = '英语';
        ```
    - 查看每个班级名以及对应的班主任名字
        ```sql
        SELECT c.name, t.name
        FROM class c
        JOIN teacher t ON c.teacher_id = t.id;
        ```
    - 查看 '王克晶' 所带班级的女同学都有谁，列出老师名字，班级名字，学生名字、学生性别
        ```sql
        SELECT t.name, c.name, s.name, s.gender
        FROM teacher t
        JOIN class c ON t.id = c.teacher_id
        JOIN student s ON c.id = s.class_id
        WHERE t.name = '王克晶'
        AND s.gender = '女';
        ```
    - 查看 '刘苍松' 所带班级的学生的英语成绩分别是多少，分数降序
        ```sql
        SELECT t.name, c.name, s.name, su.name, sss.score
        FROM teacher t
        JOIN class c ON t.id = c.teacher_id
        JOIN student s ON c.id = s.class_id
        JOIN t_stu_subject_score sss ON s.id = sss.stu_id
        JOIN subject su ON su.id = sss.subject_id
        WHERE t.name = '刘苍松'
        AND su.name = '英语'
        ORDER BY sss.score DESC;
        ```
    - 查看全校来自每个城市的学生各多少人
        ```sql
        SELECT l.name, COUNT(*)
        FROM student s
        JOIN location l ON s.location_id = l.id
        GROUP BY l.name;
        ```


#### **外连接（LEFT、RIGHT、UNION）**

外连接可以将 **关联查询** 中 **不满足连接条件的记录** 也查询出来。

- **分类：**
    - *左外连接：*
        - 左外连接是以*JOIN左侧表*作为驱动表（该表中的*所有满足过滤条件的记录*全部展示到结果集上），当来自*JOIN右侧表中的记录不满足连接条件*时，结果集中*该记录来自右侧表中的字段值*全部补NULL。
    - *右外连接：*
        - 右外连接是以*JOIN右侧表*作为驱动表（该表中的*所有满足过滤条件的记录*全部展示到结果集上），当来自*JOIN左侧表中的记录不满足连接条件*时，结果集中*该记录来自左侧表中的字段值*全部补NULL。
    - *UNION：*
        - *左外连接  UNION  右外连接*
        - UNION可以将多个DQL查询语句结果集 *取并集* 并在一个结果集里展示。
        - 并集的多个DQL语句的*SELECT子句*查询的字段：*个数、顺序、类型 要完全一致*。
        - 多个DQL查询的结果集的*交集部分仅保留一份*。
- 例：
    - 查看每个班级的名字和班主任的名字，如果没有班主任也要把班级列出来
        ```sql
        SELECT c.name, t.name
        FROM class c
        JOIN teacher t ON c.teacher_id = t.id;
        上述SQL不会将不满足连接条件的记录查询出来
        
        希望将class表作为驱动表（该表满足过滤条件的记录全部列出来）
        SELECT c.name, t.name
        FROM class c
        LEFT JOIN teacher t ON c.teacher_id = t.id;
        ```
    - 查看每个老师和他所带班级的信息，如果老师没带班，也要列出来
        ```sql
        SELECT c.name, t.name
        FROM class c
        RIGHT JOIN teacher t ON c.teacher_id = t.id;
        ```
    - 查看每个老师和他所带班级的信息，没班主任的班级要看，没带班的老师也要看
        ```sql
        SELECT c.name, t.name
        FROM class c
        LEFT JOIN teacher t ON c.teacher_id = t.id
        UNION
        SELECT c.name, t.name
        FROM class c
        RIGHT JOIN teacher t ON c.teacher_id = t.id;
        ```


#### **自连接**
- 在*同一张表*中*一条记录*可以*对应多条记录*。
- 自连接的设计一般是用来：*保存同属性的数据*且*存在上下级关系*的*树状结构数据*时使用。
- *理解*：可以把上下级关系看成多个表。
- 例如：
    - 电商中的分类树
    - 一个公司的员工组织架构
- **特点：**
    - 在*同一张表中*，*既有主键*，*也有外键*，且 **外键字段存储的值 就是 该表主键字段的值**。
    - ![[自连接.png]]
- 例：
    - 查看每个老师和他的领导是谁
        ```sql
        关联查询写法：
        SELECT t.name, m.name
        FROM teacher t, teacher m
        WHERE t.manager = m.id;
        
        内连接写法：
        SELECT t.name, m.name
        FROM teacher t
        JOIN teacher m ON t.manager = m.id;
        
        全部老师都列出
        SELECT t.name, m.name
        FROM teacher t
        LEFT JOIN teacher m ON t.manager = m.id;
        ```
    - 查看 '刘苍松' 的下属都是谁
        ```sql
        SELECT t.name, m.name
        FROM teacher t
        JOIN teacher m ON t.manager = m.id
        WHERE m.name = '刘苍松';
        ```
    - 查看刘苍松的下属所带班级的名称分别是什么
        ```sql
        SELECT m.name, t.name, c.name
        FROM teacher m
        JOIN teacher t ON t.manager = m.id
        JOIN class c ON c.teacher_id = t.id
        WHERE m.name = '刘苍松';
        ```
    - 查看3年级2班的班长是谁?
        ```sql
        SELECT c.name, l.name
        FROM class c
        JOIN student s ON s.class_id = c.id
        JOIN student l ON s.team_leader = l.id
        WHERE c.name = '3年级2班'
        GROUP BY c.name;
        ```
    - 刘苍松的下属所带班级的班长都是谁?
        ```sql
        SELECT m.name, t.name, c.name, l.name
        FROM teacher m
        JOIN teacher t ON t.manager = m.id
        JOIN class c ON c.teacher_id = t.id
        JOIN student s ON s.class_id = c.id
        JOIN student l ON s.team_leader = l.id
        WHERE m.name = '刘苍松'
        GROUP BY C.name; 
        ```
    - 年龄最大的学生所在班的班主任的上司是谁?
        ```sql
        SELECT s.name, c.name, t.name, m.name
        FROM student s
        JOIN class c ON s.class_id = c.id
        JOIN teacher t ON c.teacher_id = t.id
        JOIN teacher m ON t.manager = m.id
        WHERE s.birth = (SELECT MIN(birth)
                         FROM student);
        ```
    - 刘苍松的下属所带的班级各多少人?
        ```sql
        SELECT m.name, t.name, c.name, COUNT(*)
        FROM teacher m
        JOIN teacher t ON t.manager = m.id
        JOIN class c ON c.teacher_id = t.id
        JOIN student s ON s.class_id = c.id
        WHERE m.name = '刘苍松'
        GROUP BY c.name;
        ```
