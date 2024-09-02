
Lombok框架可以通过*特定的注解*，在 ***编译期*** 生成 **Setter、Getter、equals()、hashCode()、toString()、无参构造、全参构造等**。
主要用于简化pojo类的编写。

优势：
- 简化代码。
- 方便增减属性。

此框架依赖项为：
```xml
<!-- Lombok的依赖项，主要用于简化POJO类的编写 -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.20</version>
    <scope>provided</scope>
</dependency>
```

接下来，在*任何POJO*的 **类上**，添加 `@Data` 注解，即可在*编译期*生成全套的*Setter、Getter、equals()、hashCode()、toString()、无参构造* 等这些方法（需要当前类的父类存在无参数构造方法）。

- 关于使用Lombok时常用的**相关注解**：
    - `@Data`：添加在**类**上，在 ***编译期*** 生成 **Setter、Getter、equals()、hashCode()、toString()、无参构造**。
        - 使用此注解时，要求当前类的**父类** *必须存在无参构造方法*。
        - *没有全参构造*，如果需要用到，使用`@AllArgsConstructor`注解。
            - 加这个注解就 **必须添加** *无参构造注解* `@NoArgsConstructor`注解。
    
    - `@Setter`：添加在**类中的属性**上，可以生成**此属性**对应的*Setter方法*。
      也可以添加在**类**上，生成该类里面**所有属性**对应的Setter方法。
      
    - `@Getter`：用法与作用参考 *@Setter 注解* ，此注解的作用是，生成Getter方法
      
    - `@EqualsAndHashCode`：添加在**类**上，可以生成基于此类中**所有属性**的`equals()`和`hashCode()`方法。
      
    - `@ToString`：添加在**类**上，可以生成基于此类中**所有属性**的`toString()`方法。
        - 此注解可以配置 `callSuper` 属性，用于表示是否调用*父类的 toString方法*。
      
    - `@NoArgsConstructor`：添加在**类**上，可以生成*无参构造*。
    - `@AllArgsConstructor`：添加在**类**上，可以生成*全参构造*。
      
    - `@slf4j`：添加在**类**上，*生成log变量*，来进行*日志输出*。
    - 更多注解，可以自行查阅资料，或在IntelliJ IDEA中查看Lombok插件的i详情

**注意：** **需要**在 Intellij  IDEA中**安装 Lombok插件**，
否则，在编写代码时，不会有相关的提示，并且，即使直接写出代码，也会报错，但是，并不影响运行。
安装教程： http://doc.canglaoshi.org/doc/idea_lombok/IDEA-5-PLUGINS-LOMBOK.html

**提示：** Lombok是一个由 “*侵入性*” 的框架，当一个团队中有成员使用了Lombok，安装了Lombok插件，那么其他成员也必须得安装Lombok插件。

