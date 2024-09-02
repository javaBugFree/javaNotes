
- 创建工程时需要勾选 *Mybatis Framework* 和 *MySQL Driver*
- 创建完工程，如果pom.xml里面的 *mysql相关依赖报错* 的话，使用下面的依赖*替换* 掉，*刷新maven*即可解决
    ```xml
    <!-- 连接MySQL数据库的依赖 -->
    <dependency>  
    <groupId>mysql</groupId>  
    <artifactId>mysql-connector-java</artifactId>  
    <version>8.0.15</version>  
    </dependency>
    ```

- 在*application.properties配置文件* 中添加 *连接数据库的信息*
```properties
    # 数据库连接地址
    spring.datasource.url= jdbc:mysql://localhost:3306/bootdb?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
    # 数据库用户名&密码
    spring.datasource.username= root
    spring.datasource.password= root
```
