
在Spring Boot项目中，当需要实现Web开发（例如*使用Controller接收客户端的请求并响应*）时，需要添加 `spring-boot-starter-web` 依赖项（在创建项目时，勾选*Web*本质上也是添加此依赖），
此依赖项中包含了`spring-boot-starter`这个基础依赖项，所以，在现有的项目中，只需要将`spring-boot-starter`改为`spring-boot-starter-web`即可
```xml
<!-- SpringBoot Web依赖项 -->  
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
</dependency>
```

当**添加了以上依赖项**，你的项目会有以下明显的变化：
- 启动项目时，会*自动占用* **Tomcat**（你的项目也会部署到这个Tomcat上），默认占用 **8080 端口**。
- 在 **sec/main/resources** 下的 **static** 文件夹会是*默认的* **静态资源文件夹**，在此文件夹下的资源（例如网页、图片等）可以通过HTTP协议的URL直接访问。
    - 如果创建项目时勾选了 *Web* 依赖项，会自动创建此文件夹，如果创建项目时未勾选 *Web* 依赖项，而是手动添加 `spring-boot-starter-web` ，不会自动创建此文件夹，但可以手动创建，是等效的。


<hr>

## 指定占用端口号

在配置文件中，可以通过 `server.port` 属性指定Tomcat占用的端口号。

**提示：** 端口号的**最大值**是 **65535** ，并且，一般*不推荐使用* **低位端口(2位数 / 3位数)**，
- 因为一些低位端口号是*默认*用于*特定的服务*的，
    - 例如：
        - *21* 端口用于FTP服务，
        - *110* 端口用于邮件服务，
        - *80* 端口用于HTTP服务，等等。

例如，在 **application-dev.yml** 中添加配置：
```yaml
# 服务器配置
server:
  # 服务端口
  port: 8090
```


<hr>

## 使用控制器接收并处理 “添加相册” 的请求

在项目的根包下创建 **controller 包** ，在该包里创建 *Controller 类*，
创建 **AlbumControlelr 类** ，在类上添加 `@RestController` 注解，在类中自动装配 `AlbumService` 的对象，并且，自定义方法处理 *添加相册* 的请求：
```java
@RestController
public class AlbumController{
	
	@Autowired
	private AlbumService albumService;
	
	// http://localhost:8080/album/add-new?name=Test001&description=Test001&sort=88
	@RequestMapping("/album/add-new")
	public String xxx(AlbumAddNewDTO albumAddNewDTO){
		try{
			albumService.addNew(albumAddNewDTO);
			return "添加相册成功！";
		} catch (ServiceException e){
			return e.getMessage();
		}
	}
}
```

完成后，启动项目，可以通过URL： http://localhost:8090/album/add-new?name=Test001&description=Test001&sort=88 测试访问，也可以通过修改此URL中的参数值多次访问，
当成功添加相册数据时，在浏览器中可以看到 *添加相册成功！* 的字样，
如果相册名称被占用，可以看到 *添加相册失败，相册名称已经被占用！* 的字样。

**关于以上自动装配的 AlbumService**：
- 不建议声明为具体的实现类，那样不利于代码 **解耦**。
- *能用接口就不用抽象类*。

