
在Spring MVC中，存在统一处理异常的机制，具体表现为：**无论哪个** *处理请求的过程* 中 *出现异常*，**每种类型的异常** *只需要* **编写一段处理异常的代码** 即可！

**只会处理Controller抛出的异常！**

<br>
统一处理异常的核心是 **定义处理异常的方法**：
- **返回值类型**：可参考 *处理请求的方法*。  
- **方法名称**：自定义。
- **参数列表**：*至少包含* *被处理的异常对象*，另外，可按需添加`HttpServletRequest`、`HttpServletResponse`等少量特定的参数，当存在多个参数时，各参数不区分先后顺序。  
- **注解**：必须添加`@ExceptionHandler`注解，表示此方法是处理异常的方法。
<br>

使用该机制时，异常的位置顺序：Service -> Controller ->Spring MVC


## 运行机制

**运行机制：**
1. 当客户端向服务器端提交请求，例如“添加相册”，如果服务器端的控制器中处理请求的方法调用Service方法时出现异常，但是，*处理请求的方法* **不必处理异常**，例如：
```java
public JsonResult add(AlbumAddNewDTO albumAddNewDTO) {  
    log.debug("开始处理【添加相册】的请求，参数：{}", albumAddNewDTO);
    
	// 此次调用可能出现异常，但并不需要处理
    albumService.addNew(albumAddNewDTO);
    return JsonResult.success();    // 不需要响应成功时的描述
}
```
当处理请求的方法不使用`try...catch`捕获异常，*如果出现异常*，相当于此处理请求的方法**会抛出异常**！


2. 在Spring MVC框架中，*处理请求的方法* 都是 **被框架所调用**的，*谁调用谁得到异常*。
所以，处理请求的方法*抛出的异常* 也 *都会被框架捕获到*，则框架会 *自动* 的 *使用此异常对象*来**调用处理异常的方法**！类似于：
```java
try {  
    框架.addNew(); // 框架调用了控制器类中处理异常的方法  
} catch (ServiceException e) {  
    框架.handleServiceException(e); // 框架调用了处理异常的方法  
}
```

**需要注意**：如果将处理异常的方法 *定义在某个控制器类中*，将 *只能作用于当前控制器类* 所有处理请求时出现的异常，并不能作用于其他控制器类中处理请求时出现的异常！
例如：
```java
public class AlbumController{
	// ...
	@ExceptionHandler  
	public JsonResult handleServiceException(ServiceException e) {  
	    return JsonResult.fail(ServiceCode.ERROR, "出错啦~~~！");  
	}
}

```


3. 在使用Spring MVC框架时，可以在类上添加 `@RestControllerAdvice`注解，
此类中**特定的方法**（*例如统一处理异常的方法(`@ExceptionHandler`)*）将 *可以作用于* **整个项目中**任何处理请求的过程中！
在项目的根包下创建`ex.handler.GlobalExceptionHandler`类，作为当前项目的 **全局异常处理器 类**，将 *统一处理异常的代码* 编写在此类中（各控制器类中不再需要重复的处理异常的方法）：
```java
/**  
 * 包含 统一处理异常方法  
 */  
@Slf4j
@RestControllerAdvice   // 【关键】
public class GlobalExceptionHandler {
	
    @ExceptionHandler
    public JsonResult handleServiceException(ServiceException e) {
        log.warn("程序运行过程中出现ServiceException，将统一处理！");
        log.warn("异常信息：{}", e.getMessage());
        // 返回 响应给 客户端的 状态码 和 异常信息
        return JsonResult.fail(e);
    }
}
```


4. 在处理异常的类中，关于处理异常的方法：
    - *允许 同时存在若干个* 处理异常的方法，但各方法处理的异常必须不同。
    - 多个处理不同异常的方法，所处理的异常*允许 存在父子级关系*，在实际处理时，*优先*按照*最匹配*的类型的方法进行处理。
    - 强烈建议添加处理`Throwable`类型异常的方法，*避免 因为某个异常未被处理，导致服务器端响应500错误*。
        - 在此方法中，应该 *记录异常的类型 和 关键信息*，并且，执行到此方法时，应该及时添加新的、处理对应异常的方法。
        ```java
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


5. 另外，关于异常信息的描述，应该是“谁抛出，谁描述”的原则，
    所以，应该在`ServiceException`类中添加带`String message`的构造方法，
    并且通过父级类别的构造方法将参数用于对`detailMessage`属性（定义在`Throwable`类中）赋值，后续，可以通过`getMessage()`方法获取此值，例如：
```java
// 关键需要此构造方法  
// message 可以被 该异常的对象 使用 .getMessage() 获取到  
public ServiceException(ServiceCode serviceCode, String message) {  
    super(message);  
    this.serviceCode = serviceCode;  
}
```


6. 然后，在业务实现过程中，当需要抛出异常时，封装描述文本，例如：  
```java  
// 判断查询结果是否为null  
if (album == null) {  
    // 是：数据不存在，抛出异常  
    String message = "删除相册失败，尝试删除的相册数据不存在！";  
    log.warn(message);    
    throw new ServiceException(message);
}  
```


7. 后续，在全局异常处理器中，通过异常对象可以获取所封装的描述文本，例如：  
```java  
@ExceptionHandler  
public JsonResult handleServiceException(ServiceException e) {  
    log.warn("程序运行过程中出现ServiceException，将统一处理！");  
	log.warn("异常信息：{}", e.getMessage());  
	// 返回 响应给 客户端的 状态码 和 异常信息  
	return JsonResult.fail(e);
}  
```


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