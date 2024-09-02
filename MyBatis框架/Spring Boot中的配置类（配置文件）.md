
Spring Boot 中的**主配置文件**，就是Spring Boot的*启动类*，可以作为配置文件使用。

如果将全部配置信息都放到主配置文件，就会很混乱。一般在开发中，将配置文件分开放置，相关的放在一起。
- MyBatis 放到一个文件中
- 安全配置 放到一个文件中
- ... ...

创建一个配置包 config 管理全部的配置，然后创建MyBatis的配置类，*配置类需要标注 @configuration*
```java
/**
 * 创建一个配置文件，MyBatisConfig
 * 在配置文件中，启动Mapper接口扫描功能
 * Mapper 接口扫描功能会自动创建 Mapper接口的实现对象（使用的JDK动态代理技术）
 * Configuration 配置，用于声明 Spring的配置类
 */
@MapperScan(basePackages = "cn.tedu.boot2209.mapper")
@Configuration
public class MyBatisConfig {
}
```
