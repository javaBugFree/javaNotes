
JSON 是一种 *轻量级* 的 **数据交换格式**。

客户端和服务器之间进行**复杂的数据传输**时，如果每次传输都自己定义传输格式的话，太影响开发效率，**JSON是一种** ***通用的数据交换格式***，使用JSON可以*大大提高开发效率*。

**格式**：
- `{}`：表示*一个对象*
- **属性**：`"属性名":"数值"`
- `[]`：多个对象用 “\[]” 框起来，对象与对象之间用 `,` 分隔。

如果要*传输多个信息*，JSON中的格式为
```json
[{"name":"tom", "age":18, "gender":"男"},
 {"name":"tom", "age":20, "gender":"女"}]
```

<hr>

当客户端请求数据时，服务器端应该**响应数据**，但是，不同的请求，需要响应的数据类型可能不同，
所以应该使用泛型
```java
public class JsonResult<T> implements Serializable {    // 使用泛型
	
	// 其他代码不关心！
	
	/**  
	 * 操作成功时响应的数据  
	 * 使用泛型（类型占位符）  
	 */  
	@ApiModelProperty("操作【成功】时响应的数据")  
	private T data;     // 泛型（任意字母） T=Type, E=Element, K=Key, V=Value
	
	/**  
	 * 成功时 给客户端的响应  
	 *  
	 * @return JsonResult对象  
	 */  
	public static <T> JsonResult<T> success() {    // 静态方法使用泛型
	    return success(null);  
	  
	}  
	public static <T> JsonResult<T> success(T data){   // 静态方法使用泛型  
	    JsonResult jsonResult = new JsonResult();  
	    jsonResult.setState(ServiceCode.OK.getValue());  
	    jsonResult.setData(data);  
	  
	    return jsonResult;  
	}
}
```