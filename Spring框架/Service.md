
## Service 的基本作用

业务。

Service是项目中非常重要的组件，
*主要作用*：用于**设计 业务流程、业务逻辑**，以保障 *数据的完整性、有效性、安全性*。

Service在代码中的作用是：*调用Mapper*、*被Controller调用*

*网页流程*：**客户端** -> **Controller** -> **Service** -> **Mapper** -> **DB**

**各层的作用和代码内容**：
*Mapper层*：数据的访问，对数据的增删改查。
*Service层*：设计业务流程和业务逻辑。
*Controller层*：接收请求和响应结果。

**始终一次只解决一个问题！** 不相关的先不管。


## Service 规范

在《阿里巴巴Java手册》中 **强制** 规定：
```text
【强制】对于 Service 和 DAO 类，基于 SOA 的理念，暴露出来的服务一定是接口，内部  
的实现类用 Impl 的后缀与接口区别。  
正例：CacheServiceImpl 实现 CacheService 接口。
```


## 关于Service接口中的方法

- **为什么要用接口**：
    - *解耦*。
    - **为什么不用抽象类**：
        - *类* 应该描述的是 *类别/分类*，而 *接口* 描述的是一种 *规范/标准/行为特征*。
        - 重写的标准就被定义下来了。
        - 类于类是 *is* 的关系（子类 is 父类），
           实现类与接口是 *has* 的关系（实现类 has 接口）。



在Service接口中声明的抽象方法，应该：
- **返回值类型**：仅*以操作成功为前提*来设计返回值类型。
    - 如果是为*操作失败*，可以通过 *抛出 自定义异常* 来提示。让 业务实现类(测试类) 来try、catch区分。例如：
        ```java
        try {
            封装的类型 xxx = service.login(xx,xx);    // 处理登录成功的后续操作
        } catch (用户名错误异常 e) {    // 处理用户名错误的后续操作
        } catch (密码错误异常 e) {    // 处理密码错误的后续操作
        } catch (账号状态异常 e) {    // 处理账号状态错误的后续操作
        }
        ```
- **方法名称**：*自定义*。
- **参数列表**：绝大部分情况下，以*客户端提交的请求参数*为参考。
    - 并且，可以*按需封装*，使用封装的POJO类型（*DTO*）作为业务方法的参数。

<hr>

## 关于抛出异常

当使用抛出异常的方式来表示 **操作失败** 时，
**不可以** 使用任何*已有的异常*（JDK中原生的异常类型，及添加的依赖项中包含的异常类型），
**必须自定义异常** ，则后续捕获并处理时，一定符合抛出异常时的情景。
因为有可能 抛出已有的异常时，其他 应该抛出该异常 的 错误代码 也会被处理。
<br>

自定义异常 **必须继承自** *已有的 某个异常类型*，强烈建议继承自`RuntimeException`，其原因有：
1. **原因1**：
    - 在项目中将使用 **全局异常处理器** 统一处理异常，*要想统一处理*，则Service组件、Controller组件*都必须抛出异常*，*才能 由Spring MVC框架 捕获到异常*，进而通过全局异常处理器进行统一的处理！
    - `RuntimeException`不会受到异常的相关语句约束（*不处理时，不需要声明抛出*），
      而 **非**`RuntimeException` 一旦被抛出，*方法的声明*、*方法的调用者的声明* 等 *都需要声明抛出此异常*，由于抛出异常是固定的做法，没有必要在各个方法上都声明抛出此异常，所以，应该使用`RuntimeException`
2. **原因2**：配合Spring JDBC框架实现事务管理！

**具体步骤**：
1. 在项目的根包下创建 **ex 包**，并在其内，创建 **ServiceException** 类，继承自 `RuntimeException`：
```java
/**  
 * 业务异常  
 *  
 * @author 0659 
 * @version 0.0.1 
 */
public class ServiceException extends RuntimeException{
	
	private ServiceCode serviceCode;  
	  
	// 关键需要此构造方法  
	// message 可以被 该异常的对象 使用 .getMessage() 获取到  
	public ServiceException(ServiceCode serviceCode, String message) {  
	    super(message);  
	    this.serviceCode = serviceCode;  
	}  
	  
	// serviceCode 可以通过此方法被获取搭配  
	public ServiceCode getServiceCode() {  
	    return serviceCode;  
	}
	
}
```

2. 每次抛出异常，都应该是出现了某种 *错误*，应该对此 *错误* 进行描述，一般是 **谁抛出，谁描述**。
应该在自定义异常类中添加基于父类异常的 带`String message`参数的*构造方法*，例如：
```java
public class ServiceException extends RuntimeException{   
    // 关键需要此构造方法  
    // message 可以被 该异常的对象 使用 .getMessage() 获取到  
    public ServiceException(String message) {  
        super(message);  
    }  
}
```

3. 然后，**抛出异常时**，应该 *通过* 以上 *构造方法* 封装对 *异常的描述*：
```java
String name = albumAddNewDTO.getName();
int countByName = albumMapper.countByName(name);
if (countByName > 0) {
    // 如果已经被占用，抛出异常，会直接结束该方法
    String message = "添加相册失败，相册名称已经被占用";
    log.warn(message);
    throw new ServiceException(message);    // 【调整】在构造方法中封装message
}
```

4. 后续，**捕获异常的位置**，将不再需要“猜测”出现异常的原因，而是*从捕获到的异常对象* 中 *直接获取以上封装的描述信息* 即可：
```java
try {
    service.addNew(album);
    log.debug("添加相册成功！");
} catch (ServiceException s) {
	// 【调整】从异常对象中获取信息
    log.debug("捕获到异常，其中的消息：{}", s.getMessage());
}
```

<hr>

## 在Service中实现 “添加相册”

1. 项目的*根包下*创建 **service** 包，

2. 先在 **service 包** 下，创建 **AlbumService 接口**，并在接口中添加 "添加相册" 的抽象方法：

    ```java
    // 该DTO类用来封装 抽象方法传递的参数
    @Data
    public class AlbumAddNewDTO implements Serializable {
        private String name;
        private String description;
        private Integer sort;}
    
    // 接口
    public interface AlbumService {
	    /**
		 * 添加相册
		 *
		 * @param albumAddNewDTO 新的相册数据
		 */
		void addNew(AlbumAddNewDTO albumAddNewDTO);
	}
    ```

3. 再在 **service 包** 下创建 **impl 包** ，该包 *里面的类* 用来*实现* **service 包** 下的*接口*，
4. 然后在 **impl 包** 创建 **AlbumServiceImpl 类**，实现以上接口，重写接口中的抽象方法。

5. 添加相关的*业务规则*
    - 检查相册名称是否已经被占用，统计结果是否为0，为0没被占用
        ```sql
        SELECT COUNT(*) FROM pms_album WHERE name=?
        ```