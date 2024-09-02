
在Spring MVC框架中，`@RequestMapping` 注解的主要作用是配置 *请求路径* 与 *处理请求方法* 的映射关系。

在 `@RequestMapping` 的源代码中，可以看到它的声明
```java
@Target({ElementType.TYPE, ElementType.METHOD})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Mapping  
public @interface RequestMapping {  
    // 暂不关心内部代码  
}
```
声明上的 `@Target` 注解表示当前注解（`@RequestMapping`）可以添加在哪里。
`@Target`注解参数为`{ElementType.TYPE, ElementType.METHOD}`，表示可以添加在 *类型* 上，也可以添加在 *方法* 上。
<br>
通常，建议在每个 *Controller* **类上** 都使用 `@RequestMapping` 来配置当前类中 **多个处理方法** 的 *请求的路径* 的 **共有部分** 例如：
```java
@RestController  
@RequestMapping("/album")    // 【重要】  
public class AlbumController {  
  
    // http://localhost:9080/album/add-new
    @RequestMapping("/add-new")
    public String addNew() {        
	    // 暂不关心方法的实现
        return null;
    }
    // http://localhost:9080/album/delete
    @RequestMapping("/delete")
    public String delete() {
	    // 暂不关心方法的实现
        return null;
    }
}
```
<br>
在配置路径时，Spring MVC会*自动处理*必要的 `/` ：**如果缺少**，则*添加*；**如果多余**，则*去除*。
例如，以下几种配置是完全等效的：
| 类上的配置值 | 方法上的配置值 |
| ------------ | -------------- |
| /album       | /add-new       |
| /album       | add-new        |
| /album/      | /add-new       |
| /album/      | add-new        |
| album/       | /add-new       |
| album/       | add-new        |
| album        | /add-new       |
| album        | add-new        |
 具体使用哪组配置，并没有明确的要求，只需要 **保证** *同一个项目* 中的 *风格统一* 即可。例如推荐使用第一组，或 最后一组。另外，*第一组 或 最后一组* 在 *阅读理解上 没有歧义*。


## 源代码

- 在`@RequestMapping`的源代码中还有：
```java
String[] value() default {};
```
- `value()` 表示：此注解中可以配置*名为`value`的属性*，
    - 例如：`@RequestMapping(value = ???)`
- `String[]` 表示：`value`属性的*值类型*，
    - 例如：`@RequestMapping(value = {"a", "b"})`。
- `default {}` 表示：`value`属性的*默认值*是空数组。

在Java语言中，`value`是注解的*默认属性*，当配置注解参数时，*只配置`value`这1个属性*时，可以不必显式的写出属性名称，例如以下2种配置是完全等效的：
```java
@RequestMapping(value = {"a", "b"})
// 等效于
@RequestMapping({"a", "b"})
```

在Java语言中，如果需要配置的注解 **属性的值** 是 *某种数组类型* 的，但是当前只配置1个值（*数组只有1个元素*）时，可以*不必使用 大括号*将值框住，例如以下2种配置是完全等效的：
```java
@RequestMapping(value = {"a"})
// 等效于
@RequestMapping(value = "a")
```

所以，在以上的**Controller 类**中，配置的`@RequestMapping("/add-new")`其实配置的就是注解的`value`属性！
<br>
在`@RequestMapping`的源代码中，`value`属性的声明上还有`@AliasFor`注解的配置：
```java
@AliasFor("path")  
String[] value() default {};

@AliasFor("value")  
String[] path() default {};
```
以上，`value`属性的`@AliasFor`表示当前属性（`value`）等效于同注解内的`path`属性！
同时，`path`属性的`@AliasFor`也表示（`path`）属性也等效于`value`属性！
<br>
在`@RequestMapping`的源代码中，还有`method`属性：
```java
RequestMethod[] method() default {};
```
此`method`属性的作用是：**限制 客户端提交请求时的 请求方式**，当没有配置此属性时，所有的方式都允许。
此属性的值类型是 `RequestMethod`（是一种枚举类型）的*数组*。
例如可以配置为：
```java
@RequestMapping( value = "/add-new", method = RequestMethod.POST)
```

如果要 *允许限制多种* 请求方式，可以将 `method 的属性值` 配置为 *数组*，例如：
```java
@RequestMapping( value = "/add-new", method = {RequestMethod.POST, RequestMethod.GET})
```

在同一个项目中，允许存在*多个 请求方式 不同*，但 *请求路径 相同* 的配置，例如：
```java
@RequestMapping( value = "/add-new", method = RequestMethod.POST)  
public String add1(AlbumAddNewDTO albumAddNewDTO) {  
    // 暂不关心内容  
}
// 和
@RequestMapping( value = "/add-new", method = RequestMethod.GET)  
public String add2(AlbumAddNewDTO albumAddNewDTO) {  
    // 暂不关心内容
}
```

## 简化 限制请求方式的配置

为了 **简化 限制请求方式** 的 *配置语法*，Spring MVC 框架还提供了基于 `@RequestMapping` 的衍生注解，例如：

- `@GgetMapping()`
   ```java
    @Target(ElementType.METHOD)  
    @Retention(RetentionPolicy.RUNTIME)  
    @Documented  
    @RequestMapping(method = RequestMethod.GET)  
    public @interface GetMapping {
    	// 内部各属性 都是和 @RequestMapping 中的同名属性等效，
    	// 并且，没有声明 method属性
    }
    ```
    - 以上`@GetMapping`就 **相当于** 是将请求方式限制为`GET`的`@RequestMapping`，
        - 并且，其源代码中的`@Target`的参数值决定了它 *只能添加在方法上*。

与`@GetMapping`类似的注解还有：`@PostMapping`、`@PutMapping`、`@DeleteMapping`、`@PatchMapping`。

## 小结

- 在 *控制器类上* 推荐使用 `@RestController`。
- 在 *控制器类上* 推荐使用 `@RestMapping` 配置URL前缀。
- *各处理请求的方法上* 推荐使用 `@GetMapping / @PostMapping...` 等限制请求方式的注解来配置URL。

