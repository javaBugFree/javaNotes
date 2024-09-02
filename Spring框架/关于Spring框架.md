
## 1. 关于Spring框架

Spring框架主要解决了 **创建对象、管理对象** 的相关问题。

例如 配置类、controller、service的实现类、全局异常处理器 等的对象。

所有由Spring框架创建的对象，会被Spring框架*保存在* `ApplicationContext`（应用程序上下文）中，后续，当需要这些对象时，Spring会*自动*从`ApplicationContext`中*取出并使用*。

由于Spring的`ApplicationContext`中会保存若干个对象，所以通常也称之为 **Spring容器**。

Spring框架的基础依赖项是`spring-context`。

- Spring框架的**核心**包括：
    - IOC：Inversion of Controller，即控制反转（之前创建对象和管理对象的控制权在自己手里，使用该框架后，把控制权交给框架）
    - AOP：[[Spring AOP]]

<hr>

## 2. Spring框架创建对象的方式

### **1. 组件扫描**

在配置类（添加了`@Configuration`注解的类）上使用`@ComponentScan`注解，即可实现：当**加载此配置类时**，Spring框架会*执行组件扫描*，扫描*特定的包* 及其*子孙级包*下是否存在*组件类*，如果找到组件类，则Spring框架会自动创建此组件类的对象。

如果使用`@ComponentScan`时，*没有配置 注解参数*，*扫描的* **根包** 就是**当前配置类(项目的启动类，也是配置类) 所在的包**；
使用`@ComponentScan`注解时，还可以配置此注解的`basePackages`/`value`属性来指定需要扫描的若干个包。

- 为什么*日常*写SpringBoot项目时*没加*`@ComponentScan`注解也可以：
    - 在Spring Boot项目中，启动类上添加了`@SpringBootApplication`注解，此注解使用了`@ComponentScan`和`@SpringBootConfiguration`作为其*元注解*。
    - 所以，在默认情况下，Spring Boot启动时就会执行组件扫描！

如果被扫描的类，添加了*组件相关注解*，此类就会被视为 *组件类*，
- 在*Spring框架中*，组件注解有：
    - `@Component`：通用注解。下面三个注解的元注解都有它
    - `@Service`：建议添加在业务实现类上。
    - `@Controller`：建议添加在控制器类上。
    - `@Repository`：建议添加在存取数据的类（例如对数据库做增删改查的类）上。功能性几乎没有，只是为了@AutoWired不报错。
    - 以上四个注解，在Spring框架中，*作用、用法* 是*完全相同、完全等效*的，也就是这四个可以随便换着用。只存在语义的不同！
    
    - 在*Spring中*，还有一个非常特殊的组件注解：
        - `@Configuration`
        - 但是，Spring框架会使用“CGLib代理模式”来处理！
    
    -   在*Spring MVC中*，还新定义了注解用于标识组件类，这些新的注解都是*基于*`@Component`注解：
        - `@RestController`：一般用于Controller类
        - `@ControllerAdvice`
        - `@RestControllerAdvice`：一般用于统一异常处理器
      
     - 在*Spring Boot中*
        - `@SpringConfiguration`

总结：创建类的条件：
1. 在根包下
2. 添加组件注解

<br>

### **2. `@Bean`方法**

在*配置类中*，可以**自定义方法**，
1. *返回特定类型的对象（就是你希望Spring创建对象的类型）*，
    - 即：方法运行后，方法的 *返回值* 会自动保存到Spring容器中。
2. 并且，在方法体中，自行编写可以创建出此类型对象的代码
3. 最后在*此方法上*添加`Bean`注解
则Spring框架加载此配置类时，会*自动调用*这些方法，从而得到方法返回的对象。
```java
@Configuration  
public class BeanConfiguration {  
    @Bean  
    public AlbumController albumController() {        
	    return new AlbumController();    
    }    
}
```


### 3. *关于用法的选取*

如果需要 Spring 创建对象的类 **是自定义的**，推荐优先选取 *”组件扫描“* 的做法。

如果需要 Spring 创建对象的类 **不是自定义的**，则无法选取 ”组件扫描“ 的做法，因为你无法在别人写的类上添加组件注解，只能使用 ”*@Bean方法*“ 的做法




### 4. 关于Spring Bean的名称

由Spring框架*管理的对象*均可称之为*Spring Bean*。
每个Spring Bean都有名称。

- 如果使用组件扫描创建的对象，Spring Bean默认的名称*取决于类型的名称*：
    - 当类名的第1个字母是大写的，且第2个字母是小写的，则Spring Bean的名称就是将类名的首字母改为小写。
        - 例如 *CategoryServiceImpl*类的Spring Bean名称就是 *categoryServiceImpl* .
    - 如果**不满足以上条件**，则Spring Bean名称*就是类名*。
    - 另外，如果需要**自定义** *为其他名称*，可以*配置组件注解的 参数*，例如`@Service("categoryService")`。

- 如果使用`@Bean`**方法**创建的对象，Spring Bean的名称*默认*是 *@Bean方法名称*，也可以配置`@Bean`注解的参数，例如`@Bean("categoryService")`。

## 3. Spring框架创建的对象的作用域

由Spring框架创建的对象，*默认*是 **”单例“** 的。
如果希望某个Spring Bean不是单例的，可以通过`@Scope`注解进行配置。

**注意：** Spring Bean的 **表现特征** *可能*是*单例* 的，但Spring*并不是*通过*单例模式来实现的*。

单例（单一实例）：在任意时刻，此类型的对象最多只有一个。例如：
```java
public class Singleton() {
    // 创建的唯一对象 设为静态，因为static不能直接访问非static。
	private static Singleton instance = new Singleton();  
	
	private Singleton() {}   // 构造私有化
	
	public static Singleton getInstance() {    // 获取实例方法 静态化
		return instance
	}
}
```


可以通过`@Scope`注解 **修改** Spring管理的对象的 **作用域**，
在此注解中配置参数值为`prototype`时，对象将不再是单例的，与普通的局部变量的作用域相同，
当此注解的参数值时`singleton`时，对象是单例的，由于单例是默认的状态，所以，通常不会使用到此值。

如果是通过**组件扫描** 的方法来*创建对象*，则在*类的声明上*使用`@Scope("prototype")`注解，例如：
```java
@Scope("prototype")
@RestController
public class AlbumController(){
}
```

如果是通过 **@Bean** 的方法来*创建对象*，则在*方法的声明上*使用`@Scope("prototype")`注解，例如：
```java
@Configuration  
public class BeanConfiguration {  
    @Bean  
	@Scope("prototype")    
    public AlbumController albumController() {        
	    return new AlbumController();    
    }   
}
```


由Spring框架创建的对象，如果是 **”单例“** 的。*默认*是**饿汉式单例模式**。
- 如果*希望*被创建的单例对象是“*懒汉式*”的，可以通过`@Lazy`注解进行配置。与`@Scope`注解的使用方式相同。
    - 如果是通过*组件扫描*创建的Spring Bean，则在组件类上添加`@Lazy`
    - 如果是通过`@Bean`方法创建的Spring Bean，则在`@Bean`方法上添加`@Lazy`

*示例：饿汉式单例模式*
```java
public class Singleton() {
	private static Singleton instance = new Singleton();  
	
	private Singleton() {}
	
	public static Singleton getInstance() {
		return instance
	}
}
```

*示例：懒汉式单例模式*（可能出现多线程问题）
```java
public class Singleton() {
	private static Singleton instance = null;  
	
	private Singleton() {}
	
	public static Singleton getInstance() {
		if(instance == null) {    // 判断有没有必要锁
			synchronized("java") {    // 加锁，防止发生多线程问题
				if(instance == null) {    // 判断有没有必要创建对象
					instance = new Singleton();
				}
			}
		}
		return instance
	}
}
```

## 4. 自动装配

Spring的自动装配机制表现为：当**组件类的属性，或被Spring自动调用的方法的参数** *需要值*时，使用特定的做法，可以使得Spring使用容器的对象为这些量自动赋值。

最常见的用法是 在*类的属性上* 使用`@Autowired`注解，即可表示 “需要自动装配” 的意义，则Spring框架会尝试为此属性执行自动装配。
例如：
```java
@RestController
public class CategoryController {

	@Autowired
	private CategoryService categoryService;    // 注意：Spring容器中必须存在匹配的对象
	// 或
	@Autowired  
	private CategoryMapper categoryMapper;
}
```

其实，还可以使用`@Resource`注解代替`@Autowired`注解，在以上代码中，执行效果是相同的。


- 关于`@Autowired`与`@Resource`的 ***区别***：
    
    - `@Resource`是Java注解（是*javax.annotation*包中的），`@Autowired`是Spring框架的注解。
        - 如果你不使用Spring框架，而是使用其它框架实现自动装配，`@Resource`注解均可使用，而仅当使用Spring时才可以使用`@Autowired`。
            - 由于Spring框架普及程度非常高，所以使用`@Autowired`是完全没有问题的
              
    - `@Resource`注解是 *优先按照* **名称** 进行匹配，*无匹配则 再* **检查类型**是否相同；
    - `@Autowired`注解是 *优先按照* **类型** 查找匹配的对象，*如果 存在多个类别匹配的*，*再*尝试**根据名称**进行匹配。
        - 提示：如果有且仅有1个类型匹配的Spring Bean，这2个注解都可以成功装配，如果匹配类型的Spring Bean超过1个，只要其中1个能匹配名称，这2个注解都可以成功装配，如果都不匹配名称，这2个注解都无法成功装配。
          
    - 当使用`@Resource`时，可以配置此注解的`name`属性，用于指定Spring Bean的名称，例如：
        ```java
        @Resource(name = "categoryServiceImpl")
		private ICategoryService categoryService;
		```
    - 当使用`@Autowired`时，只能匹配使用`@Qualifier`注解来指定Spring Bean的名称，例如：
        ```java
        @Autowired
		@Qualifier("categoryServiceImpl")
		private CategoryService categoryService;
		```
        - 注意，以上使用到的`@Qualifier`注解还可以添加到方法的参数上！


- 关于`@Autowired`的 ***装配机制详情***：
    - *先根据类型*查找匹配的Spring Bean的数量
        - **0个**：取决于`@Autowired`注解的`required`属性：
            - `true`：*装配失败*，启动项目或启动测试时就会报错。
            - `false`：*放弃装配*，在启用时不会报错，此时尝试自动装配的量的值为*null*。后续可能出现*NPE（空指针异常）*
        - **1个**：*直接装配*，且*成功*。
        - **多个**：将尝试*按照名称*进行匹配，即被自动装配的量的名称，需要与某个Spring Bean的名称完全相同。
            - 如果*存在*匹配名称的Spring Bean：装配*成功*。
            - 如果*不存在*匹配名称的Spring Bean：装配*失败*，启动项目或启动测试时就会报错。



## 5. DI：关于为属性注入值的做法

Spring **通过 DI 来实现 / 完善了 IOC。**

Spring在处理自动装配（依赖注入）时，有*多种装配机制*：

- **字段注入**：*在属性上* 添加`@Autowired`注解
    - 例如：
        ```java
        @Autowired  
		private CategoryService categoryService;
		```


- **Seter注入**：*属性*所对应的*set方法*。在*方法上*添加`@Autowired`，并在*方法体*中*为属性赋值*。
     - 例：
        ```java
        @RestController  
		public class CategoryController {  

		    private CategoryService categoryService;  
		    @Autowired    
		    public void setCategoryService(ICategoryService categoryService) {        
			    this.categoryService = categoryService;    
			}    
		}
        ```


- **构造方注入**：在 *被Spring调用的 构造方法上* 添加 *参数*，并通过此参数为属性赋值。
  使用 *组件扫描* 来创建Spring Bean时，Spring框架会*自动调用*组件类的*构造方法*，规则如下：
    - 如果类*没有*显式的添加构造方法，则会自动调用*默认的构造方式*。
    - 如类中*仅有1个*构造方法，不必添加`@Autowired`注解，无论是否有参数，Spring都会尝试自动调用。
    - 如类中有*多个*构造方法，默认情况下，Spring会*自动尝试*调用*无参数构造*方法（如果存在的话）
    - 如类中有*多个*构造方法，*希望*Spring*调用* **特定的某个**构造方法，需要在此构造方法上添加`@Autowired`注解。
    - 例如，注入CategoryService的对象：
        ```java
        @RestController  
		public class CategoryController {  
		  
		    private CategoryService categoryService;  
		    
		    public CategoryController(CategoryService categoryService) {        
			    this.categoryService = categoryService;    
		    }
		}
		```


**注意：** 理论上*并不推荐* **字段注入**，所以，在IntelliJ IDEA中也有相关提示：
![[@Autowired不推荐的提示.png]]






## 6.关于Spring Bean的生命周期

在Spring Bean*归属的类中*，可以自定义“**初始化**”和“**销毁**”的生命方法，
则Spring框架会在*创建对象之后*自动调用“*初始化*”的生命周期方法，并在*销毁对象之前*调用“*销毁*”的生命周期方法。

- 关于**自定义的方法**：  
    - 应该是 *公有的*。
    - 应该是 `void`*返回值类型*。
      - *销毁方法* 可以使用 *布尔类型* 的返回值。
    - 方法*名称*是*自定义*的。
    - *参数列表*应该*为空*。

仅当添加了`@PostConstruct`注解的方法，才是“**初始化**”的生命周期方法，此方法会在*构造方法之后* 自动执行。  
仅当添加了`@PreDestroy`注解的方法，才是“**销毁**”的生命周期方法，此方法会在*销毁当前类的对象之前* 自动执行。
例如：  
```java
// 初始化
@PostConstruct
public void init() {
    log.debug("执行生命周期方法：CategoryController的初始化方法");
}

// 销毁
@PreDestroy
public void destroy() {
    log.debug("执行生命周期方法：CategoryController的销毁方法");
}
```

## 7.关于 IoC 和 DI

**IoC（Inversion of Control）：** *控制反转*，即将各对象的控制权交给了Spring框架（之前创建对象和管理对象的控制权在自己手里，使用该框架后，把控制权交给框架）

**DI（Dependency Injection）**：*依赖注入*，即为各依赖项注入值
  
Spring框架负责的 **创建对象、管理对象** 都是*Spring IoC的表现*。
  
Spring框架 **通过DI完成了IoC**，即 **DI** 是 *实现手段*，**IoC** 是 *需要实现的目标*。


## 8.关于Spring AOP

[[Spring AOP]]