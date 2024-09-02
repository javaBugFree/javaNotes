
### 什么是XML映射

- 使用**注解**的映射SQL的问题
    - *长SQL*需要*拆行*，不方便维护
    - *动态SQL查询*拼接复杂(不同时机使用不同的字段查询)
    - 源代码中的SQL，不方便与*DBA协作*（检查优化sql，让整体项目最终效果更好）
        - 程序员写的sql，往往没有专业DBA搞的sql专业。往往程序员写完sql，再交给DBA进行优化

- MyBatis建议*使用XML文件* **映射SQL**才能*最大化发挥MySQL*的功能
    - *统一管理SQL*，方便协作
    - 不需要 "",+ 等语法，*方便 "长"SQL*
    - 方便处理*动态SQL连接*

参考链接：https://mybatis.org/mybatis-3/zh/sqlmap-xml.html



### 开发步骤：

1. 创建项目（不用使用SpringBoot3！）*选择依赖*：
    1. web-starter
    2. MyBatis
    3. JDBC 驱动
    4. Spring-JDBC
    5. ![[创建XML映射时勾选的依赖.png]]

2. 配置**application.properties**，
    - *设置数据库连接*，*数据库用户名和密码*，*设置xml配置文件的位置*
    - 如果创建的工程 使用的是*阿里云(http://start.aliyun.com)的版本*，会*自带 xml配置文件的位置* 的设置
    - 
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/bootdb?characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai&rewriteBatchedStatements=true
spring.datasource.username=root
spring.datasource.password=root

#指定Mybatis的Mapper文件
#设定 mapper xml 文件的位置, classpath 就是指 resources 和 java 位置
mybatis.mapper-locations=classpath:mappers/*.xml

#指定Mybatis的实体目录, 之后返回值为该entity包内的 POJO对象 时直接写 实体类名 即可
mybatis.type-aliases-package=com.example.xmldemo.entity

# 日志相关配置  
logging:  
  # 日志显示级别  
  level:  
    # 作用的包：显示级别  
    com.example.csmall: trace
```

3. 在*resources文件夹*中创建一个文件夹：*mappers*
    1. 在里面添加一个*XML文件*，文件从doc.canglaoshi.org 下载，此配置文件用来*书写SQL语句*
    2. 改名为 DemoMapper.xml
        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>  
        <!DOCTYPE mapper  
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
          <!-- namespace 的值设定为被映射的包名.类名 -->
        <mapper namespace="com.example.xmldemo.mapper.DemoMapper">  
            <!-- select 元素用于映射查询语句 到 方法，
                 id值 必须和 mapper接口中的方法名 一致！
                 需要设定返回值类型resultType，类型与方法返回值类型一致
                 select 元素中定义SQL，SQL查询结果将 自动映射到 方法返回值，
                 不要使用分号结尾！！-->
            <select id="hello" resultType="java.lang.String">
            SELECT 'Hello World!'
            </select>
        </mapper>
        ```

4. 创建*mapper.DemoMapper接口*
    - **抽象方法的声明原则**：
        
        - *返回值类型*：如果要执行的数据访问操作，是 **增、删、改** 类型的，使用 `int` 作为返回值，表示 *受影响的行数*。
            - 如果 *不关心受影响的行数* ，也可以使用 `void` 作为返回值，但并*不推荐*。
            - 如果要执行数据访问操作是 *查询类型* 的，只要保证返回值类型能够 **放得下** 查询结果即可。
            - **提示：** *非统计类型* 的**查询**，一般*不建议使用 实体类* 来接收数据，应该使用其他**POJO类型**，《阿里巴巴Java开发手册》提供的参考：
                ```text
                1） 数据对象：xxxDO，xxx 即为数据表名。 
                2） 数据传输对象：xxxDTO，xxx 为业务领域相关的名称。 
                3） 展示对象：xxxVO，xxx 一般为网页名称。 
                4） POJO 是 DO/DTO/BO/VO 的统称，禁止命名成 xxxPOJO。
                ```
                - 另外，关于POJO的后缀，**必须每个字母都是 大写的**，《阿里巴巴Java开发手册》强制规定：
                ```text
                【强制】类名使用 UpperCamelCase 风格，必须遵从驼峰形式，但以下情形例外：DO / BO / DTO / VO / AO 
                正例：MarcoPolo / UserDO / XmlService / TcpUdpDeal / TaPromotion 
                反例：macroPolo / UserDo / XMLService / TCPUDPDeal / TAPromotion
                ```
        - *方法名称*：自定义。根据《阿里巴巴Java开发手册》提供的*参考*：
            ```text
            1） 获取单个对象的方法用 get 做前缀。 
            2） 获取多个对象的方法用 list 做前缀。 
            3） 获取统计值的方法用 count 做前缀。 
            4） 插入的方法用 save/insert 做前缀。 
            5） 删除的方法用 remove/delete 做前缀。 
            6） 修改的方法用 update 做前缀。
            ```
        - *参数列表*：取决于要执行的**SQL语句中有哪些参数**，如果参数的数量*超过1个*，且具有明确的相关性，建议使用封装的类型（*DTO*）作为抽象方法的参数，
            - 通常，插入单条数据、修改数据时，都使用实体类作为抽象方法的参数
            - 如果涉及**批量数据操作**，例如批量删除、批量更新、批量插入等，可以使用*数组、可变参数 或  `List`集合* 表示若干个参数值。
    
    - 注意：
    1. **接口名** 和 *xml文件* 的 **namespace** 一致。
    2. **方法名** 和 *xml文件* 的 **select元素的 id** 一致。
    3. **方法返回值类型** 和 **resultType 的值** 一致。
    ```java
    /**  
     * 编写Mapper接口，用于映射SQL语句  
     */
    @Mapper  
    public interface DemoMapper {  
        String hello();
    }
    ```

5. 编写*MyBatis配置文件* config.MyBatisConfig
    1. 包名、文件名，没有限制！
    2. 设置完该配置文件之后，就**不用** *每一个Mapper映射文件* 里面 *都添加@Mapper注解* 了
    3. 文件中使用 @MapperScan 扫描 mapper包
        `@MapperScan(basePackage= mapper包的源根路径 )`
    3. @Configuration 配置，用于声明 Spring的配置类
    ```java
    /**  
     * 创建一个配置文件，MyBatisConfig  
     * 在配置文件中，启动Mapper接口扫描功能：@MapperScan
     * Mapper 接口扫描功能会自动创建 Mapper接口的实现对象（使用的JDK动态代理技术）
     * @Configuration 配置，用于声明 Spring的配置类
     */
    @MapperScan(basePackages = "com.example.xmldemo.mapper")  
    @Configuration  
    public class MyBatisConfig {  
    }
    ```

6. 测试案例: 测试结果说明，SQL被执行了，方法返回了SQL语句的结果  
    ```java
    @SpringBootTest  
    public class DemoMapperTests {  
        
        @Autowired
        DemoMapper demoMapper;
        
        @Test    
        void test(){        
            String str = demoMapper.hello();
            System.out.println(str);
        }
    }
    ```