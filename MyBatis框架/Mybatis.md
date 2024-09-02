
Mybatis框架是目前最流行的**数据持久层框架**

使用Mybatis框架可以帮助程序员***自动生成 JDBC代码***，程序员只需要通过 **注解接口类 或 xml配置文件** 提供 *需要执行的SQL语句，以及对象和表的映射关系(对应关系)*，Mybatis框架会根据此映射关系和SQL*自动生成 JDBC代码*，从而提高开发效率。

Mybatis框架属于**ORM框架**，*Object Relational Mapping* **对象关系映射**，指 *Java对象* 和 *数据库中表* 的*对应关系*，Mybatis框架就是通过两者之间的关系，生成JDBC代码。



## Mybatis框架如何使用

Mybatis框架*主要的作用*就是帮我们**自动生成JDBC代码**。
而想要实现这一点就要通过 **由@Mapper注解的接口类** 来实现。

**@Mapper注解：** 此注解是**将当前接口** *设置为* **映射接口**，*Mybatis框架*会根据*此接口中的内容*，生成对应的*实现类*，在*实现类里面生成 对应的 JDBC代码*。

- 而在接口类中，**sql语句**的 *"?"* 的 *赋值方式* 也有所改变。
    - 先在一个方法上写注解 `@Insert() / @Select....`
    - 然后在注解中添加sql语句`"insert into product values(null, #{title}, #{price}, #{num})"`
        - sql语句中 *"?"* 变成 **#{要替换的变量}**
    - **#{xxx}：** 会从 *方法的参数列表* 中找到 *同名变量*，如果 *没有* 同名变量 则调用 **对象里面的** *同名getXxx方法*。
    - 例如：
        - **插入**数据
            ```java
            @Mapper
            public interface ProductMapper{
                
                /*先在接口中的一个方法上添加注解 @Insert()，
                  再在注解中添加sql语句*/
                @Insert("insert into product values(null,#{title},#{price},#{num})")  
                void insert(Product product);   
                // 参数列表中没有同名变量，所以调用对象中的同名 getXxx方法
                
            }
            ```
        - **查询**数据
            ```java
            // 查询数据  
            @Select("SELECT id, title, price,num FROM product")  
            List<Product> select();  
            /* 生成的JDBC代码 会自动 将查询到的数据 封装到Product对象中，
               然后再将Product对象装到List集合中，
               最后再return返回。*/
            ```

