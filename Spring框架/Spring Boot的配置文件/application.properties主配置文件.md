

在Spring Boot项目中，在 **src/main/resources** 文件夹下存在 **application.properties** 文件，此文件是 Spring Boot 会自动读取的配置文件。

Spring Boot定义了许多特定用途的配置，在`appication.properties`中，需要*按照特定的属性名进行配置*，则Spring Boot**读取**到了这些特定的配置后，会自动应用起来！(*并没有去连接*，数据库用户名密码随便输入都可以运行成功)

添加连接数据库的配置：
```properties
spring.datasource.url=jdbc:mysql://localhost:3306//mall_pms?characterEncoding=utf-8&useUnicode=true&serverTimezone=Asia/Shanghai  
spring.datasource.username=root  
spring.datasource.password=root
```
