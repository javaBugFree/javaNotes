
关于RESTful（通常简称为REST）：

>【百度百科】RESTFUL是一种网络应用程序（服务器端）的设计风格和开发方式，基于[HTTP](https://baike.baidu.com/item/HTTP/243074?fromModule=lemma_inlink)，可以使用[XML](https://baike.baidu.com/item/XML/86251?fromModule=lemma_inlink)格式定义或[JSON](https://baike.baidu.com/item/JSON/2462549?fromModule=lemma_inlink)格式定义。RESTFUL适用于移动互联网厂商作为业务接口的场景，实现第三方[OTT](https://baike.baidu.com/item/OTT/9960940?fromModule=lemma_inlink)调用移动网络资源的功能，动作类型为新增、变更、删除所调用资源。

**重点**：RESTful是一种*设计风格*，并不是规范或标准，所以，并不一定需要完全照做。

style：杀马特，free style

- RESTful风格的 **典型特征** 有：
    - 服务器端处理请求后，是*响应正文*的，是前后端分离的（不直接响应整个页面）。
    
    - 可能将某些具有唯一性，且不敏感的参数值设计为URL的一部分。例如：
        `https://blog.csdn.net/gjs935219/article/details/102531854`
        如果采取传统的做法，以上URL应该设计为：
        `https://blog.csdn.net/article/details?username=gjs935219&id=102531854`
    
    - *同一个URL*，可能 **用于多种不同** 的 *数据处理*，当然，*请求方式会被设计为不同的*（增：`POST` / 删：`DELETE` / 改：`PUT` / 查：`GET`），例如：
        - 请求路径：`/album/`，请求方式：`POST`，操作：**添加**相册数据。
        - 请求路径：`/album/9527`，请求方式：`DELETE`，操作：**删除**id值为 *9527* 的相册数据。
        - 请求路径：`/album/9527`，请求方式：`PUT`，操作：**修改**id值为 *9527* 的相册数据。
        - 请求路径：`/album/9527`，请求方式：`GET`，操作：**查询**id值为 *9527* 的相册数据。
        
        - **注意：** 这种设计的做法在绝大部分业务系统中并不适用。在*业务系统*中，通常*仍只是采用* `GET`和`POST`这2种请求方式。
           并且，以**查询为主要目的**的请求会设计为`GET`，*除此以外*的所有请求都会设计为`POST`.
        
        - **提示：** 这种设计通常用于*对数据源直接操作*的场景，只适合单一操作，例如在Elasticsearch的学习中可以接触到。也就是说 **通常不照做**。

<br>

Spring MVC 框架很好的支持了RESTful，
1. 在设计URL时，可以 **使用** `{自定义名称}` 的*占位符*表示此处应该是某个参数值。
    - 例如：`@PostMapping("/{id}/delete")`
    - 当设计成以上格式，则客户端提交请求时，占位符位置无论是是什么值，都可匹配上以上设计的路径！
      
2. 然后，在处理请求的方法的参数列表中，添加对应此占位符参数值的请求参数，并且，在此*请求参数上*使用 `@PathVariable` 注解，例如：
    - `@pathVariable` 注解：表示从*占位符中* **同名的参数** 获取值。
    ```java
	@PostMapping("/{id}/delete")
	public ??? delete(@PathVariable Long id) {
	    // 暂不关心方法的实现
	}
	```
	
    - 如果*占位符中的名称* 和 *请求参数的名称* **不一致**，需要在`@PathVariable`注解中配置占位符名称，而请求方法的参数名称就不重要了，例如：
    ```java
	@PostMapping("/{id}/delete")
	public ??? delete(@PathVariable("id") Long albumId) {
	    // 暂不关心方法的实现
	}
	```
	
    - **需要注意**：如果客户端提交的请求路径中，占位符位置的内容 *无法被转换成 Long类型* 时，将出现 *400错误*。
      - 400错误是接受、获取了占位符位置的值 并 尝试进行转换类型时出现的错误。


- 在设计占位符时，可以在**占位符名称的右侧**、在*右大括号的左侧* 添加1个**冒号**，并在冒号的右侧可以添加 ***正则表达式***，以**限制参数值的基本格式**，
    - 正则表达式要求*不必特别严格*，因为还有 *Valication框架* 在。
    - 例如：
    ```java
	@PostMapping("/{id:[0-9]+}/delete")
	public ??? delete(@PathVariable Long id) {
	    // 暂不关心方法的实现
	}
	```
    - 如果客户端提交的请求路径中，占位符位置的内容 *不匹配以上正则表达式* 时，将出现 *404错误*。
        - 而404错误则不会去转换类型，只要不符合正则表达式的限制，直接响应404页面。
        - 404错误 *优于* 400错误，*省资源*。因为404是接受时就报错，而400是接受后转换时才报错。


- 其实，*多个使用了占位符*、*不同规则* 的URL，是 **允许共存** 的。
    - 例如：根据*占位符值的不同*，执行不同的方法
    ```java
	// http://localhost:8080/album/id
	@GetMapping("/{id:[0-9]+}")
	public xxx getStandardById(@PathVariable Long id){
		// 暂不关心方法的实现
	}
	
	// http://localhost:8080/album/name
	@GetMapping("/{name:[a-z]+}")
	public xxx getStandardByName(@PathVariable Long name){
		// 暂不关心方法的实现
	}
	```
	
    - 需要注意，使用这种做法，*必须保证* **URL只能匹配其中1个正则表达式**，
       如果使用了同一个请求能够匹配上2个使用了占位符及正则表达式的配置，则会出现500错误，例如：
    ```java
	// http://localhost:8080/album/id
	@GetMapping("/{id:[0-9]+}")
	public xxx getStandardById(@PathVariable Long id){
		// 暂不关心方法的实现
	}
	
	// http://localhost:8080/album/name
	@GetMapping("/{name:[0-9a-z]+}")
	public xxx getStandardByName(@PathVariable Long name){
		// 暂不关心方法的实现
	}
	```
    在以上配置中，如果占位符的值，是纯数字的，将可以匹配以上2个正则表达式，则SpringMVC框架无法选取，将出现错误！


- 另外，*不使用占位符的配置*，与*使用了占位符的配置*，是*允许共存*的，**精准匹配**。例如：
    - 当提交了对应 `/test` 的请求时，会执行第2个方法，即**精准匹配**的方法，而不会执行使用了占位符的方法。
    - 根据占位符的值调用不同的方法，为test时执行test方法，其他字母执行另一个方法。
```java
@GetMapping("/{name:[a-z]+}")  
public ??? getStandardByName(@PathVariable String name) {  
    // 暂不关心方法的实现  
}  
  
@GetMapping("/test")  
public ??? testStandardByName(String name) {  
    // 暂不关心方法的实现  
}
```

- 在开发实践中，如果没有明确的规定（例如在开发团队中尚未决定使用什么样的URL风格），可以参考：
    - 获取数据*列表*：`/数据类型的复数`
        - 例如：`/albums`
    
    - 根据 id 获取*某条数据*：`/数据类型的复数/{id}`
        - 例如：`/album/{id:[0-9]+}`
        - 其实，在RESTful的建议中，查询、修改、删除都用以上这种URL，根据请求方式来区分需要执行的数据管理操作
    
    - 根据 id 对某条数据*执行某种操作*：`/数据类型的复数/{id}/命令`，或 `/数据类型的复数/{id}/数据属性/命令`
        - 例如：`/album/{id:[0-9]+}/delete` 
            或 `/album/{id:[0-9]+}/password/update`
