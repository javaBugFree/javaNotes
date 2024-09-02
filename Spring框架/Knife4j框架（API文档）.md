
Knife4j 是一款**在线API文档框架**，可以 **基于当前项目的控制器类** *中的配置* 生成文档，并自带调试功能。

API：应用程序的公共接口，对接代码。

- 在项目中使用Knife4j需要*3个步骤*：
    1. 添加**依赖**：
        - **注意：** 本次使用的Knife4j的版本，要求项目中的 **Spring Boot 版本**必须是*2.6以下版本*（不包含2.6）。否则Knife4j也必须升级版本。
    2. 在主配置文件中开启 **增强模式**
        - 配置 `knife4j.enable` 属性，取值为 `true`。
    3. 添加**配置类**
        - 是相对固定的diamagnetic，复制粘贴即可，但 **需要** *改配置的包名*。
        - **注意：** 需要检查配置类中所*配置的包名*，必须是当前项目中 *控制器 所在的包*。
<br>
1. 在 **pom.xml** 添加依赖：
```xml
<!-- Knife4j Spring Boot：在线API -->
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>2.0.9</version>
</dependency>
```

2. 在 **application.yml** 中添加配置：
```yml
# Knife4j配置  
knife4j:  
  # 开启增强模式  
  enable: true
  # 屏蔽生产环境 (为true则，生产环境不可访问该文档)  
  production: false
```

3. 添加配置类：在项目的根包下的`config`包中，创建`Knife4jConfiguration`配置类：
```java
package com.example.csmall.product.config;  
  
import com.github.xiaoymin.knife4j.spring.extension.OpenApiExtensionResolver;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import springfox.documentation.builders.ApiInfoBuilder;  
import springfox.documentation.builders.PathSelectors;  
import springfox.documentation.builders.RequestHandlerSelectors;  
import springfox.documentation.service.ApiInfo;  
import springfox.documentation.service.Contact;  
import springfox.documentation.spi.DocumentationType;  
import springfox.documentation.spring.web.plugins.Docket;  
import springfox.documentation.swagger2.annotations.EnableSwagger2WebMvc;  
  
/**  
 * Knife4j配置类  
 *  
 * @author java@tedu.cn 
 * @version 0.0.1 
 */
@Slf4j  
@Configuration  
@EnableSwagger2WebMvc  
public class Knife4jConfiguration {  
  
    /**  
     * 【重要】指定Controller包路径  
     */  
    private String basePackage = "com.example.csmall.product.controller";  
    /**  
     * 分组名称  
     */  
    private String groupName = "product";  
    /**  
     * 主机名  
     */  
    private String host = "http://java.tedu.cn";  
    /**  
     * 标题  
     */  
    private String title = "酷鲨商城在线API文档--商品管理";  
    /**  
     * 简介  
     */  
    private String description = "酷鲨商城在线API文档--商品管理";  
    /**  
     * 服务条款URL  
     */    
     private String termsOfServiceUrl = "http://www.apache.org/licenses/LICENSE-2.0";  
    /**  
     * 联系人  
     */  
    private String contactName = "Java教学研发部";  
    /**  
     * 联系网址  
     */  
    private String contactUrl = "http://java.tedu.cn";  
    /**  
     * 联系邮箱  
     */  
    private String contactEmail = "java@tedu.cn";  
    /**  
     * 版本号  
     */  
    private String version = "1.0.0";  
    @Autowired  
    private OpenApiExtensionResolver openApiExtensionResolver;  
  
    public Knife4jConfiguration() {  
        log.debug("创建配置类对象：Knife4jConfiguration");  
    }  
  
    @Bean  
    public Docket docket() {  
        String groupName = "1.0.0";  
        Docket docket = new Docket(DocumentationType.SWAGGER_2)  
                .host(host)  
                .apiInfo(apiInfo())  
                .groupName(groupName)  
                .select()  
                .apis(RequestHandlerSelectors.basePackage(basePackage))  
                .paths(PathSelectors.any())  
                .build()  
                .extensions(openApiExtensionResolver.buildExtensions(groupName));  
        return docket;  
    }  
  
    private ApiInfo apiInfo() {  
        return new ApiInfoBuilder()  
                .title(title)  
                .description(description)  
                .termsOfServiceUrl(termsOfServiceUrl)  
                .contact(new Contact(contactName, contactUrl, contactEmail))  
                .version(version)  
                .build();  
    }  
}
``` 

完成后，启动项目，通过 **doc.html** 即 http://localhost:8080/doc.html 进行访问
<br>
- 为了更好的显示API文档（方便别人阅读），应该**通过**相关**注解进行配置**
    - `@Api`：添加在 *控制器类* **类上**，通过此注解的 `tags 属性`，可配置 **管理模块的名称**。
        - **自定义顺序**：根据 **字典顺序排序（0~9，a~z，A~Z）** 在配置 管理模块名称 时，可以在各*名称 前面添加* **数字或字母** 进行序号的升序排序，达到自定义顺序的目的。
    <br>
    - `@ApiOperation`：添加在 *控制器类* 中 **处理请求的 方法上**，通过此注解的`value`属性，可配置 **业务的名称**
      <br>
    - `@ApiOperationSupport`：添加在 *控制器类* 中 **处理请求的 方法上**，，通过此注解的`order` 属性( *int* )，可自定义 **配置业务的顺序**。*升序排序*。
        - **小技巧：** 推荐*属性值都 给大一点*，中间预留出添加别的业务的空间。
            例如：`@ApiOperationSupport(order = 100 / 200 / 300)`
     <br>
    - `@ApiModelProperty`：添加在 *POJO类型* 的 **属性上**，配置 **参数说明**
        - 通过此注解的 `value 属性` ，可配置 **请求参数的描述文本**。
        - 通过此注解的`required`属性，可配置 **是否必须提交此参数**（但不具备检查功能，即你不提交还是能运行）。
        - 通过此注解的 `example 属性` ，可配置 **示例值**，
            - 需要*注意 数据类型 的问题*，例如数值类型的属性不要配置字母或符号作为示例值
        - 通过此注解的 `dataType 属性`，可配置 **数据类型**，但此属性*通常不需要配置*。
        - 例如：
            ```java
            /**  
			 * 相册的DTO类  
			 */
            @Data  
            public class AlbumAddNewDTO implements Serializable {
	            
	            // 相册名称
	            @ApiModelProperty(value = "相册名称", required = true, example = "相册01")  
	            private String name;
	            
	            // 排序序号
	            @ApiModelProperty(value = "排序序号", required = true, example = "99")  
			    private Integer sort;
	            
            }
            ```
        <br>
    - `@ApiImolicitParam`：添加在 *控制器类* 中 **处理请求的 方法上**，用于对 ***未封装的请求参数*** 进行配置 **参数说明**。
        - `name 属性`：**指定** 配置 *哪个参数*。
        - `value 属性`：配置指定的那个参数的*说明*。
        - `dataType 属性`：配置 **数据类型**，此注解上显示的数据类型 **默认**是 `String`，对于*非字符串类型 的属性*，应该显式的配置`dataType`属性
        - 例如：
            ```java
            @ApiImplicitParam(name = "id", value = "相册ID", required = true, example = "9527", dataType = "long")  
            private String delete(Long id) {  
                return null;  
            }
            ```
    
    - `@ApiImplicitParams`：添加在 *控制器类* 中 **处理请求的 方法上**，当方法的 *未封装参数超过1个*，且 *有 多个 需要配置`@ApiImplicitParam`* 时，必须将多个`@ApiImplicitParam`注解配置作为此注解（`@ApiImplicitParams`）的参数
        - 此注解参数是 `@ApiImplicitParam` 数组类型
        - 例如：
            ```java
            @ApiImplicitParams({
                    @ApiImplicitParam(name = "id", value = "相册ID", required = true, example = "9527", dataType = "long"),
                    @ApiImplicitParam(name = "userId", value = "用户ID", required = true, example = "9527", dataType = "long")
            })
            private String delete(Long id, Long userId) {
                return null;
            }
            ```


## 关于响应结果中的null值  
  
目前，服务器端处理完请求后，会向客户端响应JSON格式的结果，但是，某些情况下，会有一些属性的值为`null`，例如，当成功的添加相册后，响应的结果是：  
  
```json  
{  
  "state": 20000,  
  "message": null
}  
```  
  
以上`message`属性是没有必要响应的，因为此属性的作用是“失败”时的提示文本，当操作成功时，此属性的值一定是`null`值，将此属性响应到客户端去是没有任何意义的！  

**解决方法有三种**：
1. 可以在响应结果对应的*属性上*配置`@JsonInclude`*注解*，例如：  
```java  
@Data  
public class JsonResult implements Serializable {  
  
    private Integer state;        
    
    // @JsonInclude用于配置“此属性什么时候会包含在JSON结果中”  
    // NON_NULL 表示 不为null的时候  
    @JsonInclude(JsonInclude.Include.NON_NULL)    
    private String message;    
}  
```  
  
2. 也可以将此*注解*添加在*类上*，则类中每个属性都是此配置：  
    - 如果项目中其他类的对象也会被控制器返回到客户端，当需要取出其他类的对象中为null的属性时，还需要在其他类上也添加相同的配置！
```java  
@Data  
@JsonInclude(JsonInclude.Include.NON_NULL) // 在类上配置，每个属性都遵循此配置  
public class JsonResult implements Serializable {  
  
    private Integer state;    
    private String message;    
}  
```  
  
3. 或者，还可以在*配置文件*中通过`spring.jackson.default-property-inclusion`属性进行作用于*整个项目的全局配置*！例如，在`application.yml`中添加：  
```yaml  
# Spring相关配置  
spring:  
  # jackson框架的相关配置  
  jackson:    # 服务器端响应JSON结果时，JSON结果中默认包含哪些属性  
    default-property-inclusion: non_null  # 表示 所有的属性都是 不为null时显示
```  
  
**提示：** 在配置文件中、在类上、在类的属性上，都可以配置“是否包含在JSON结果中”，**通常只需要在主配置文件中配置即可**，
但是，如果在多处都配置了，以 *"作用域" 更小的为准*，例如，在类上和在类的属性上都配置了，但配置值不同，则以在类的属性上的配置为准！