
## 关于Spring Validation  
  
Spring Validation框架是用于 *检查请求参数* 的 **基本格式有效性** 的框架，例如，检查某个请求参数是否为`null`、检查某个字符串的长度、检查某个字符串是否为空字符串、检查数字值的区间等等。

一般用于检查Controller里的方法的参数，也可以用于检查Service方法里的参数，但大部分情况下用不到。

在Spring Boot项目，添加`spring-boot-starter-validation`依赖项即可使用它：
  
```xml
<!-- Spring Boot Validation依赖项，用于检查请求参数的基本格式 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

<hr>

## 常用的检查注解

使用Validation框架检查数据的基本格式时，常用的检查注解有：  
**提示**：类型符合的参数，可以添加多个注解。
- `@NotNull`：不允许为 **null 值**，即客户端 *必须提交* 此参数。
    - 可用于任何类型的参数。
- `@NotEmpty`：不允许为 **空字符串**，即不允许是长度为0的字符串。即 *""*。
    - 仅用于字符串类型的参数。
- `@NotBlank`：不允许为 **空白的字符串**，即不允许*仅由空格、TAB制表位组成* 的字符串。
    - 仅用于字符串类型的参数。
- `@Length`：限制 **字符串的长度**。也可以用于检查集合等数据的长度，但不常见。
    - 通常仅用于字符串类型的参数。
- `@Pattern`：通过 **正则表达式** *检查字符串的格式*，此注解的 `regexp 属性` 是用来定义正则表达式的。
    - 仅用于字符串类型的参数。
- `@Min`：限制 **最小值**。最大为long类型的上限值。
    - 仅用于整型数值类型的参数
- `@Max`：限制 **最大值**。
    - 仅用于整型数值类型的参数
- `@Range`：限制 **取值区间**。默认最小值为0，最大值是 long类型的上限值。
    - 仅用于整型数值类型的参数
    - *仅当数值存在时，才执行检查*，如果调用此方法时，传入的数值为 *null*，则此注解是无效的！所以，这类注解通常与 NotNull 组合使用。
  
**提示：** 在源代码中，找到某个检查注解的`import`语句，按住Ctrl键点击此注解的*包名*，在IntelliJ IDEA的左侧面板中就会显示此包下的内容，即此包下所有的检查注解（检查注解都在同一个包中），需要注意，有2套检查注解的包，例如`@NotNull`和`@Range`这2个注解就在不同的包下。

关于检查注解的使用：
- 所有检查注解*都有* `message 属性`，用于配置 **检查失败时的提示文本**。
- 每个被检查的请求参数都 **可以同时添加多个检查注解**。
- `@NotNull 注解` 可以添加在任何类型的请求参数上，并且，除了 `@NotNull` 以外的**所有检查注解** *为Null时*，*都会视为检查通过*！或 *理解为 其他检查注解仅当数据不为Null时才会生效*。所以，许多检查规则都会同时使用 `@NotNull` 与另1个或多个检查注解，除非你允许这个参数是null值。

<hr>

## 关于检查请求参数的原则

通常，*服务器端*对*客户端提交的请求参数的值*，应该保持 **不信任** 的态度，*哪怕客户端软件*（网页、手机app等）*有严格的检查机制*，主要原因在于：
- 客户端软件是运行在用户的设备上的，存在客户端软件被篡改的可能（被破解）。
- 某些项目可能有多种不同的客户端（网页端、手机端、其他客户端），检查规则可能并不统一。
- 某些用户没有及时更新客户端软件的版本，旧版的客户端软件的检查规则与新服务器端新的规则并不对应。
所以，为了保证各请求参数的有效性，**必须在服务器端进行检查**！
**注意：** 即使服务器端存在检查请求参数的机制，**客户端仍有必要** *对即将提交的请求参数进行检查*！
- 客户端的程序是在用户的设备上离线运行，在客户端检查时，不消耗服务器资源，也不必联网，能够更快的响应用户的操作，对用户的体验更好。
- 可以阻止绝大部分错误的请求提交到服务器端，能够有效的缓解服务器端的压力。
    - 以上列举的 *服务器端不信任客户端提交的请求参数* 的原因都是极小概率的事件。

## 检查 POJO 类型的请求参数

当需要对**POJO类型**的*请求参数的格式进行检查*时，
1. 首先，需要在*处理请求的方法*的**被检查参数上**添加 `@Valid 注解` 或 `@Validated 注解`，表示需要对此参数进行检查，例如：
```java
// http://localhost:8080/album/add-new  
@PostMapping("/add-new")
//                    ↓↓↓↓↓↓ 添加注解
public JsonResult add(@Valid AlbumAddNewDTO albumAddNewDTO){
    log.debug("开始处理【添加相册】的请求，参数：{}", albumAddNewDTO);

    albumService.addNew(albumAddNewDTO);    // 此次调用可能出现异常，但并不需要处理
    return JsonResult.success();    // 不需要响应成功时的描述
}
```


2. 然后，在POJO类的*属性上*，添加你需要的 **检查注解**（根据不同的检查规则，使用不同的检查注解），例如，当某个请求参数是*必须提交*的，可以添加 `@NotNull 注解`，例如，在**AlbumAddNewDTO 类**中：
```java
public class AlbumAddNewDTO implements Serializable {
	@NotNull    // 新增的注解
	private String name;
	
	// 暂不关心其他代码
}
```
至此，已经能够实现对以上`name`属性进行 *不允许为null* 的检查，如果客户端提交的请求参数中不包含`name`属性，服务器端将响应`400`错误（请求参数格式错误）。

在使用检查注解（例如`@NotNull`）上可以配置`message`属性，用于配置 *检查不通过时的提示文本*，例如：
```java  
@NotNull(message = "添加相册失败，必须提交相册名称！")  
private String name;  
```


<hr>

## 处理BindException（检查失败时的异常）
  
当检查不通过时，服务器端除了响应`400`错误以外，默认情况下，在服务器端的控制台还会出现错误信息：
```
2023-01-03 14:06:02.125  WARN 4540 --- [nio-9080-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.validation.BindException: org.springframework.validation.BeanPropertyBindingResult: 1 errors<LF>Field error in object 'albumAddNewDTO' on field 'name': rejected value [null]; codes [NotNull.albumAddNewDTO.name,NotNull.name,NotNull.java.lang.String,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [albumAddNewDTO.name,name]; arguments []; default message [name]]; default message [添加相册失败，必须提交相册名称！]]
```  


### 进行处理

1. 先在`ServiceCode`中添加新的枚举值：  
```java
public enum ServiceCode {
	
    OK(20000),
    ERR_BAD_REQUEST(40000), // 新增
    ERR_NOT_FOUND(40400),
    ERR_CONFLICT(40900);
    
    // 暂不关心其它代码
    }
```  

<br>

2. 再在**全局异常处理器**（`GlobalExceptionHandler`）中添加对异常`BindException`的处理：
    - 需要注意：不可以直接通过 `BinExceprion` 对象调用 `getMessage()` 获取异常信息，例如以下做法就是*错误*的：
	    ```java
        @ExceptionHandler  
        public JsonResult handleBindException(BindException e) {  
            log.warn("程序运行过程中出现BindException，将统一处理！");  
            log.warn("异常信息：{}", e.getMessage());  
            String message = e.getMessage();    
            return JsonResult.fail(ServiceCode.ERR_BAD_REQUEST, message);
        }  
        ```
    - 如果按照以上处理方式，当检查不通过时，响应的结果为：  
        ```json
        {  
          "state": 40000,  
          "message": "org.springframework.validation.BeanPropertyBindingResult: 1 errors\nField error in object 'albumAddNewDTO' on field 'name': rejected value [null]; codes [NotNull.albumAddNewDTO.name,NotNull.name,NotNull.java.lang.String,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [albumAddNewDTO.name,name]; arguments []; default message [name]]; default message [添加相册失败，必须提交相册名称！]"  
        }  
        ```
    
    <br>
    
    - 应该从`BindException`对象中获取`FieldError`对象，然后，再通过`FieldError`对象的`getDefaultMessage()`获取此前在`@NotNull`这种检查注解中配置的提示文本，例如：
        ```java
        @ExceptionHandler
        public JsonResult handleBindException(BindException e) {
            log.warn("程序运行过程中出现BindException，将统一处理！");
            log.warn("异常信息：{}", e.getMessage());
            FieldError fieldError = e.getFieldError();
            String message = fieldError.getDefaultMessage();
            return JsonResult.fail(ServiceCode.ERR_BAD_REQUEST, message);}
        ```
	
	- 则检查不通过时的响应中，`message`属性中只有检查注解中配置的提示文本，例如：
	    ```json
	    {  
          "state": 40000,
          "message": "添加相册失败，必须提交相册名称！"
        }
        ```

### 多个检查注解的处理

**注意：** 如果使用了*多个检查注解*，并且，**客户端提交的请求参数** 有 *多个错误*，Validation框架会检查所有错误，并汇总所有错误信息！
例如，对所有请求参数都添加`@NotNull`注解：
```java
@Data
public class AlbumAddNewDTO implements Serializable {
	
    @NotNull(message = "添加相册失败，必须提交相册名称！")
    private String name;
    @NotNull(message = "添加相册失败，必须提交相册简介！")
    private String description;
    @NotNull(message = "添加相册失败，必须提交排序序号！")
    private Integer sort;
}
```
此时，如果暂时不使用全局异常处理器，当提交请求时，所有请求参数都不提交，异常信息为：
```
2023-01-03 14:22:42.357  WARN 15868 --- [nio-9080-exec-1] .w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.validation.BindException: org.springframework.validation.BeanPropertyBindingResult: 3 errors<LF>Field error in object 'albumAddNewDTO' on field 'description': rejected value [null]; codes [NotNull.albumAddNewDTO.description,NotNull.description,NotNull.java.lang.String,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [albumAddNewDTO.description,description]; arguments []; default message [description]]; default message [添加相册失败，必须提交相册简介！]<LF>Field error in object 'albumAddNewDTO' on field 'name': rejected value [null]; codes [NotNull.albumAddNewDTO.name,NotNull.name,NotNull.java.lang.String,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [albumAddNewDTO.name,name]; arguments []; default message [name]]; default message [添加相册失败，必须提交相册名称！]<LF>Field error in object 'albumAddNewDTO' on field 'sort': rejected value [null]; codes [NotNull.albumAddNewDTO.sort,NotNull.sort,NotNull.java.lang.Integer,NotNull]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [albumAddNewDTO.sort,sort]; arguments []; default message [sort]]; default message [添加相册失败，必须提交排序序号！]]  
```

可以看到，当出现3种错误时，3种错误的信息都会被包含在`BindException`异常对象中，如果*启用全局异常处理器*，响应结果中的提示文本`message`将是以上3种错误中 **随机** 的*某1个提示文本*，具体是哪一个，是*不可控的*！

当客户端提交的请求参数有多种错误时，可以：
- 一次性提示全部种类的错误
- 固定的提示某1种错误

当需要提示 **全部种类的错误** 时，
1. 首先，应该调整`@NotNull`注解中的提示文本，应该*不要包含 共有的部分*（否则，多段信息拼接成1个字符串后，存在多个相同的部分），例如配置为：
    ```java  
    @Data  
    public class AlbumAddNewDTO implements Serializable {  
      
        @NotNull(message = "必须提交相册名称！")  
        private String name;  
        @NotNull(message = "必须提交相册简介！")  
        private String description;  
        @NotNull(message = "必须提交排序序号！")  
        private Integer sort;  
    }  
    ```

2. 然后，在处理异常时，先通过 `BindException` 对象的 `getFieldErrors()`方法 获取所有的错误，然后，对其进行 **遍历**，并 **拼接** 每个错误中封装的 *提示文本*，例如：
    ```java
    @ExceptionHandler
    public JsonResult handleBindException(BindException e) {
        log.warn("程序运行过程中出现BindException，将统一处理！");
        log.warn("异常信息：{}", e.getMessage());
        StringJoiner message = new StringJoiner(",",  
	        "请求参数格式错误", "!");   // 可自动配置分隔符,起始和后缀
        List<FieldError> fieldErrors = e.getFieldErrors();
        for (FieldError fieldError : fieldErrors) {
	        message.add(fieldError.getDefaultMessage());
        }
        return JsonResult.fail(ServiceCode.ERR_BAD_REQUEST, message.toString());
    }
    ```

3. 最后，客户端提交多个错误参数时，错误提示为：
    ```java
    {  
	    state: 40000,    
	    message: "xxxxx" + "必须提交相册名称！必须提交相册简介！必须提交排序序号！"  
    }
    ```


### 快速失败

在使用Validation框架检查请求参数格式时，可以将其配置为 **快速失败**，即：当 *检查到第1个错误时*，就 *不再继续检查* 请求参数的格式了，而是 *直接反馈错误结果*！

反馈的错误结果是几种错误*其中某一种*！
  
关于“快速失败”的配置，需要**自定义配置类**来配置，
1. 在项目的根包下创建`config.ValidationConfiguration`类，
2. 在类上添加`@Configuration`注解，
3. 并在此类中通过`@Bean`注解配置`Validator`对象：
```java
@Slf4j
@Configuration
public class ValidationConfiguration {
	
	public ValidationConfiguration() {  
	    log.debug("创建配置类对象：ValidationConfiguration");  
	}
	
    @Bean   // 创建Validator对象，并由框架自动调用
    public javax.validation.Validator validator(){
        return Validation.byProvider(HibernateValidator.class)
                .configure()    // 开始配置
                .failFast(true)     // 配置快速失败
                .buildValidatorFactory()    // 构建Validator工厂
                .getValidator();    // 从Validator工厂中获取Validator对象
    }
}
```
完成以上配置后，再次尝试提交请求，无论请求参数有多少种错误，都 **只会提示其中固定的1种错误**！
当有了快速失败后，检查时 *最多只会有1个错误*，在处理异常时，可以不必获取所有的错误信息，而是直接获取仅有的那一个错误信息并提示出来即可！

所以，在各属性上可以使用完整的描述，例如：
```java  
@Data
public class AlbumAddNewDTO implements Serializable {
	
    @NotNull(message = "添加相册失败，必须提交相册名称！")
    private String name;
    @NotNull(message = "添加相册失败，必须提交相册简介！")
    private String description;
    @NotNull(message = "添加相册失败，必须提交排序序号！")
    private Integer sort;
}
```

并且，在处理异常时也进行调整：
```java
// 处理检查格式时错误时的异常
@ExceptionHandler
public JsonResult handleBindException(BindException e) {
    log.warn("程序运行过程中出现ServiceException，将统一处理！");
    log.warn("异常信息：{}", e.getMessage());
    // 返回 响应给 客户端的 状态码 和 异常信息
	
    // 仅会出现一种错误时，或出现多种错误 但只提示其中某1个错误
    // 先获取 filedError对象
    FieldError fieldError = e.getFieldError();
    // 根据 filedError对象 获取
    String message = fieldError.getDefaultMessage();
	
    /*
        当会出现多种错误时，并提示全部错误
        StringJoiner message = new StringJoiner("，",
                "请求参数格式错误：", "!");   // 可自动配置分隔符
		
        List<FieldError> fieldErrors = e.getFieldErrors();
        for (FieldError fieldError : fieldErrors) {
        message.add(fieldError.getDefaultMessage());
        }
    */
    
    return JsonResult.fail(ServiceCode.ERR_BAD_REQUEST, message);
}
```


<hr>

## 检查未封装的请求参数（简单类型的参数）

如果某些请求的参数数量较少，或各参数并不相关，通常不会将参数封装到POJO类型中，例如：
```java
// http://localhost:9080/albums/9527/delete  
@PostMapping("/{id:[0-9]+}/delete")  
public String delete(@PathVariable Long id) {  
    // 暂不关心方法的具体实现  
}
```

1. 当需要检查这类请求参数时，首先，需要在**当前类上**添加`@Validated`注解，例如：
```java
@RestController
@Validated // 新增
public class AlbumController {
    // 暂不关心类内部的代码
}
```

2. 然后，在 **需要检查的参数上** 添加检查注解，例如：
```java
// http://localhost:9080/albums/9527/delete  
@PostMapping("/{id:[0-9]+}/delete")  
//                   ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓ 新增的检查注解  
public String delete(@Range(min = 1, max = 10000) @PathVariable Long id) {  
    // 暂不关心方法的具体实现  
}
```
**提示**：以上 `@Range 注解` 是用于检查 *数值类型的请求参数的值* **是否在指定的区间范围内** 的。


**注意：** 这种检查方式出现的异常，与检查POJO类型的请求参数的并不相同，这种检查抛出的异常类型是`ConstraintViolationException`。

为了更好的处理异常，首先，检查注解依然需要配置`message`属性，例如：  
```java  
@PostMapping("/{id:[0-9]+}/delete")  
//                                                ↓↓↓↓↓↓↓ 配置message属性  
public String delete(@Range(min = 1, max = 10000, message = "删除相册失败，相册ID非法！")  
                         @PathVariable Long id) {    // 暂不关心方法的实现  
}  
```  
  
然后，在全局异常处理器中添加对此类异常的处理：  
```java  
@ExceptionHandler
public JsonResult handleConstraintViolationException(ConstraintViolationException e) {
    log.warn("程序运行过程中出现handleConstraintViolationException，将统一处理！");
    log.warn("异常信息：{}", e.getMessage());
    // 由于ConstraintViolationException的API的设计，只能拿到“所有错误”的集合，才能进一步拿到错误的提示文本
    Set<ConstraintViolation<?>> constraintViolations = e.getConstraintViolations();
    String message = "";
    for (ConstraintViolation<?> constraintViolation : constraintViolations) {
    message = constraintViolation.getMessage();
    }
    return JsonResult.fail(ServiceCode.ERR_BAD_REQUEST, message);
}
```  

<hr>

## 处理Throwable
随着开发进度的推进，需要处理的异常会越来越多，但是，无论添加多少处理异常的方法，仍可能存在遗漏，为了**避免因为某个异常未被处理**，*导致服务器端响应 500错误*，应该在全局异常处理中添加处理`Throwable`的方法：  
```java  
// 注意：以下方法存在的意义主要在于：避免因为某个异常未被处理，导致服务器端响应500错误  
// 注意：e.printStackTrace()通常是禁止使用的，因为其输出方式是阻塞式的！  
//      以下方法中使用了此语句，是因为用于发现错误，并不断的补充处理对应的异常的方法  
//      随着开发进度的推进，执行到以下方法的概率会越来越低，  
//      出现由于此语句导致的问题的概率也会越来越低，  
//      甚至补充足够多的处理异常的方法后，根本就不会执行到以下方法了  
//      当项目上线后，可以将此语句删除  
@ExceptionHandler  
public JsonResult handleThrowable(Throwable e) {  
    log.warn("程序运行过程中出现Throwable，将统一处理！");  
    log.warn("异常类型：{}", e.getClass());  
    log.warn("异常信息：{}", e.getMessage());  
    String message = "【开发阶段专用】你的服务器端出了异常，请检查服务器端控制台中的异常信息，并补充处理此类异常的方法！";  
    // String message = "服务器忙，请稍后再尝试！"; // 项目上线时应该使用此提示文本  
    e.printStackTrace(); // 打印异常的跟踪信息，主要是为了在开发阶段更好的检查出现异常的原因  
    return JsonResult.fail(ServiceCode.ERR_UNKNOWN, message);
}  
```  
并且，每次程序运行至此处时，都*应该检查服务器端的控制台*，*分析*问题出现的*原因*，并 **添加处理此类异常的方法**。