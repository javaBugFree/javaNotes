
面向 **对象** 的 *数据库持久化方式*。可以增加我们的开发效率

### 配置方法：

**pom.xml**文件中*添加依赖*，或着创建项目时 *勾选 Spring Data Jpa*。
```xml
<!-- springdata-jpa 操作数据库 -->  
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-jpa</artifactId>  
</dependency>
```


**application.properties** 配置文件：
```properties
# 使用springData-jpa操作数据库，为数据库映射表  
# 显示sql语句  
spring.jpa.show-sql=true  
# 格式化sql  
# 生成的sql语句我们希望打印到控制台，不格式化的话会全部一行显示  
spring.jpa.properties.hibernate.format_sql = true  
# 自动更新映射 数据库表结构  
spring.jpa.hibernate.ddl-auto=update
```


**pojo类** 中 *添加注解*：
- `@table`注解：通过jpa将当前对象映射为数据库的一个表table。
	- `(name="tb_user")`：重新映射表名为：tb_user。
- `@Entity`：与table注解组合使用，是使用jpa标准的注解。
- `@id`：将对象的某一个属性指定为主键Id。
- `@GeneratedValue`：设置主键id以*自动生成*的方式生成。
	- `(strategy=GenerationType.IDENTITY)`：指定生成策略为*自增*。
- `@column(name="")`：设置映射数据库中该属性的*字段名*。
```java
// 通过jpa将当前类映射为数据库的一个表  
@Table(name = "tb_user")    // name：重新映射为另外一个表名  
@Entity  
public class User {  
    @Id // 让userId作为主键Id  
    // 设置userid以 自动生成 的方式生成，括号为指定生成策略为自增  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    // 设置映射数据库中该属性的字段名  
    @Column(name = "user_id")  
    private Integer userId;  
    @Column(name = "user_name")  
    private String userName;  
    @Column(name = "password")  
    private String password;  
    @Column(name = "email")  
    private String email;
}
```


### 数据访问层 自动实现增删改查

使用springData-jpa时，数据访问层接口 **继承 CrudRepository<>**，即可自动实现增删改查。
- `CrudRepository<1,2>接口`：该接口中包含诸多增删改查的方法，多数情况不需要再自己编写实现。
	- `<1,*>`：需要操作哪一个*Pojo类*。
	- `<*,2>`：该Pojo类的*主键*属性的*类型*。
- **注意**：该接口的**save()方法**会根据传入的对象是否有主键id，来判断是新增还是修改，
	- **有主键id** 为 *新增*，**没有**则为*修改*。 
```java
/**  
 *  数据访问层  
 */  
@Repository  
public interface UserMapper extends CrudRepository<User,Integer> {  
  
    /*  
        使用springData-jpa时  
        继承CrudRepository接口后可以自动帮我们实现增删改查的方法  
        <1,2>：两个参数分别为：1：需要操作哪个Pojo类，2：该Pojo类的主键属性的类型  
        save()方法会根据传入的对象是否有主键id，来判断是新增还是修改，  
        有id为新增，没有则为修改。  
     */  
}
```



