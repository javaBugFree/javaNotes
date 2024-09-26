
YAML是一种编写配置文件的语法，表现为以 **.yml 或 .yaml** 作为扩展名的文件，不与 *.properties* 同时使用。

- 相比于 **.properties** 文件，其*语法的改变*有：
    - 原**属性名中有小数点的部分**，改为使用：**冒号 + 换行后 空两格**。并且，每换行一次，增加2个空格。
    - 原属性名与属性值使用*等于号分隔*，改为使用：**冒号 + 一个空格**。
    - 如果多个配置中，*属性名有相同的部分*，则不必配置相同的部分，**保持对应的空格（缩进）** 即可。

例如，原有的关于数据库连接的配置，使用YAML语法则改为：
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mall_pms?characterEncoding=utf-8&useUnicode=true&serverTimezone=Asia/Shanghai  
    username: root
    password: root
```

- **注：** YAML*语法要求是严格的*，必须按照以上语法规则，例如：
    - 每次换行后需要添加2个空格，*不可以使用TAB制表位取代*。
        **提示：** 在使用*IntelliJ IDEA*编辑 **.yml** 文件时，会**自动** *将一个TAB转换为2个空格*。
    
    - 属性名与属性值之间，冒号的右侧必须有一个空格，
        **提示：** 对于*纯数字的属性值*，如果此*属性的值应该是 字符串*时，需要使用*引号框住整个数字*
	
    - **List类型**元素赋值时，*每个元素* 都要以 “`- 加 空格`” 开头，在这个 “`-`” 之后的所有配置，都是 *同一个元素里的* 属性信息。
        - 例如：
            ```yml
            routes:
              - id: gateway-beijing
                uri: lb://beijing
                predicates: 
                  - Path=/bj/**
			```
    
    - 如果语法错误，将无法解析此类文件，在开发实践中，如果语法错误不明显，可以尝试全部删除并重写配置代码！

**注意：** 不允许存在完全相同的多行配置（包括缩进），例如不可以同时存在2行`spring:`。

**提示：** 在Spring Boot项目中，即使*没有将属性名中的小数点 换成YAML语法应该有的样子*，也*不会出错*。   例如：`spring.datasource.username: root`，也可以。

在*Spring框架*中，并*不支持*YAML语法的配置，需要添加`snakeymal`工具包，
但在Spring Boot中，基础依赖项（`spring-boot-starter`）中已经包含YAML配置的工具包（`snakeyaml`），所以，可以直接识别并解析YAML语法的配置文件。

在Spring Boot项目中，对 **.yml** 文件的支持，与对`.properties`文件的是完全相同的，即：你可以将所有 **.properties** 文件改成 **.yml**文件，再调整配置文件内部的语法即可。
