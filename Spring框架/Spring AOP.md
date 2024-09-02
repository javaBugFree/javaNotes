
**AOP：面向切面的编程**

**注意**：AOP技术源于AspectJ，*并不是Spring框架持有的技术*，只是Spring很好的支持了AOP。

AOP技术**主要解决**了 “***若干个不同的方法均需要执行相同的任务***” 的问题！

在许多框架中，**使用AOP实现了**：*事务管理*、*安全检查（权限管理）* 等等。


![[Spring AOP 相关.png]]



在项目中，数据的处理流程大概是：
```
用户登录：请求 ---> Controller ---> Service ---> Mapper
新增相册：请求 ---> Controller ---> Service ---> Mapper
属性列表：请求 ---> Controller ---> Service ---> Mapper
```

假设存在一个需求：统计处理任何请求的过程中，Service的执行耗时。
```
//                              (    切面    )
属性列表：请求 ---> Controller --(-> Service -)--> Mapper
//                              ↑            ↑
//                            切入点   ↑    切入点
//                                通知（Advice）

若使用@Around注解，流程就变成了：               
 //                         ↓      这是一个切面类     ↓
请求 ---> Controller ---> Aspect ---> Service ---> Aspect ---> Mapper
```


1. 在Spring Boot项目中要使用AOP，需要添加*依赖项*：  
```xml  
<!-- Spring Boot支持AOP编程 -->  
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-aop</artifactId>  
</dependency>
```

2. 然后，*创建切面类*，*实现AOP编程*，例如：
    - **连接点(JoinPoint)**：程序执行过程中的*某个节点*，例如调用了*某个方法*。
    - **切入点(PointCut)**：*选择*1个或多个连接点的表达式。
```java
/**  
 * 统计各Service方法执行耗时的切面  
 */  
@Aspect  // 切面  
@Component  // 组件  
public class TimerAspect {  
	
    // execution()的配置是使用AOP时的“切入点表达式”，用于匹配某些方法  
    // 在切入点表达式中，可以使用通配符  
    // -- 星号：任意1次匹配  
    // -- 2个连续的小数点：任意n次匹配，仅能用于包名和参数列表  
    // 另外，在表达式中，方法的返回值类型左侧还可以指定修饰符，修饰符是可选的  
    // 注解是典型的修饰符之一  
    //                 ↓ 返回值类型  
    //                   ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓ 包名  
    //                                                        ↓ 类名  
    //                                                          ↓ 方法名  
    //                                                            ↓↓ 参数列表  
    @Around("execution(* com.example.csmall.product02.service.*.*(..))")
    public Object timer(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("TimerAspect切面中的方法开始执行");
        String targetClassName = pjp.getTarget().getClass().getName();// 类型
        String signatureName = pjp.getSignature().getName();// 方法声明中的方法名
        Object[] args = pjp.getArgs();// 方法的参数列表
        System.out.println("类型：" + targetClassName);
        System.out.println("方法名：" + signatureName);
        System.out.println("参数：" + Arrays.toString(args));
  
        // 记录开始时间
        long start = System.currentTimeMillis();
  
        // 执行连接点方法
        // 如果是加载相册列表时，就相当于执行 albumService.list()方法
        // 注意：调用proceed()方法时，必须获取返回值，并作为当前切面方法的返回值，该返回值相当于切面内的方法的返回值。
        // 注意：调用proceed()方法时，必须抛出异常，或在捕获后再抛出异常，该异常相当于切面内方法抛出的异常
        Object result = pjp.proceed();
  
        // 记录结束时间  
        long end = System.currentTimeMillis();  
  
        // 显示执行耗时  
        System.out.println("执行耗时：" + (end - start));  
  
        // 必须返回调用proceed()方法的结果  
        return result;  
    }  
}
```




## 通知（Advice）注解

- `@Around`：**环绕连接点**，即*包裹了连接点*，你写的代码可以 *在连接点之前 和 之后执行*。
    - *注意*：只有使用 `@Around` 时，切面方法才需要 *`Object` 返回值* 和 *方法参数`ProceedingJoinPoint`*
- @Before：在 *连接点之前* 执行
- @AfterReturning：仅当连接点方法 *正常返回* 后执行，也就是说，当抛出异常时不会执行
- @AfterThrowing：仅当连接点方法 *抛出异常* 时执行，也就是说，当方法没有抛出异常，而是正常返回时并不会执行
- @After：在 *连接点之后* 执行

以上各Advice执行例如：
```java
@Around  
try {  
    @Before
    执行连接点
    @AfterReturning
} catch (Throwable e) {
    @AfterThrowing
} finally {
    @After
}
@Around
```

## 切面方法的定义

```java
通知注解("execution(连接点返回值类型 包名.类名.方法名(参数列表))")
public 返回值类型 方法名(参数) 可选择抛出方法 {}
```

`execution()` 的配置是使用AOP时的 “**切入点表达式**”，*用于 匹配某些方法*

在切入点表达式中，**可以使用通配符**
- *星号*：任意 *1次* 匹配
- *2个连续的小数点*：任意 *n次* 匹配，仅能用于包名和参数列表

例如：
```java
//                 ↓ 返回值类型  
//                   ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓ 包名  
//                                                        ↓ 类名  
//                                                          ↓ 方法名  
//                                                            ↓↓ 参数列表  
@Around("execution(* com.example.csmall.product02.service.*.*(..))")  
public Object timer(ProceedingJoinPoint pjp) throws Throwable {}
```

另外，在表达式中，连接点方法的返回值类型左侧还可以指定修饰符，修饰符是可选的  
- 注解是典型的修饰符之一



## 执行连接点方法时需注意

**执行连接点的方法**：`Object result = pjp.proceed();`

如果是加载相册列表时，就相当于执行 albumService.list()方法

**注意**：调用proceed()方法时，*必须获取返回值*，*并作为当前切面方法的返回值*，该返回值*相当于连接点的方法的返回值*。

**注意**：调用proceed()方法时，*必须抛出异常*，*或在捕获后再抛出异常*，该异常*相当于连接点方法抛出的异常*


## 获取当前连接点的信息

```java
String targetClassName = pjp.getTarget().getClass().getName();  // 类型  
String signatureName = pjp.getSignature().getName();  // 方法声明中的方法名  
Object[] args = pjp.getArgs();  // 方法的参数列表  
System.out.println("类型：" + targetClassName);  
System.out.println("方法名：" + signatureName);  
System.out.println("参数：" + Arrays.toString(args));
```