
**JDBC：Java数据库连接（ Java Database Connectivity ）**


### Java如何连接数据库

- JAVA在**JDBC**中提供一套**通用的接口**，用于**连接和操作数据库**。
    - **不同的数据库厂商** 都 提供了一套**对应的实现类**来操作自家提供的DBMS。而这套实现类也称为**连接该DBMS的驱动(Driver)**。
    
    - **使用步骤：**
        - *1：* 加载对应数据库厂商提供的*驱动* (*MAVEN添加依赖即可*)
            - 例如：
                ```java
                <!-- 连接MySQL数据库的依赖 -->  
                <dependency>  
                    <groupId>mysql</groupId>  
                    <artifactId>mysql-connector-java</artifactId>  
                    <version>8.0.15</version>  
                </dependency>
                ```
        - *2：* 基于 *标准的JDBC操作流程* 操作该数据库

- **标准的JDBC操作总流程：**
    1. *加载驱动*
        - 调用方法：`Class.forName(不同厂商提供的Driver的实现类)`
        - 例如在 *Mysql/MariaDB数据库* 中使用：
            ```java
            // 所有Mysql/MariaDB数据库中的 驱动 都是这样
            Class.forName("com.mysql.cj.jdbc.Driver");
            ```
    2. *与数据库建立连接*
        - 调用方法： `DriverManager.getConnection()` 
            ```java
            // 方法调用格式：
            Connection DriverManager.getConnection(
                String uri, String username, String password
            )
            
            /*参数1：数据库路径*/（JDBC有格式要求，但是不同厂商在规定的格式中又有自身不同的地方）
            例如 Mysql/MariaDB的uri固定格式：
                                        vvvvv 要使用的数据库名称，相当于USE birdbootdb
            jdbc:mysql://localhost:3306/mysql?birdbootdbcharacterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
            
            /*参数2：数据库的用户名*/
            例如：root
            
            /*参数3：数据库的密码*/
            例如：root
            
            /*返回值类型：Connection*/
            该方法返回的 Connection实例 就表示数据库的一条连接
            Connection是JDBC的一个 核心接口，它用于表示数据库的一个连接，里面提供了创建执行对象，关闭连接等操作。
            
            // 一个完整的方法调用 如下：使用 birdbootdb 数据库(mysql数据库)
            Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/birdbootdb?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true", "root", "root");
            ```
    3. *Statement对象是用于 执行SQL语句 的对象*
        - 调用方法：`connection.createStatement();`
            ```java
            Statement connection.createStatement()
            返回值类型：Statement
            Statement有很多方法用来执行SQL语句
            ```
    4. *执行SQL语句*
        - 