
当客户端请求数据时，服务器端应该**响应数据**，但是，不同的请求，需要响应的数据类型可能不同，
所以应该使用泛型，任何fan到泛型的地方都应该声明泛型里面装的什么类型。
1. 当 *类中的属性* 的类型*为泛型* 时，在 **类上** *也需要声明*。
2. 使用 **泛型** 来 *声明 属性*。
3. 在**静态方法上**，如果使用了泛型，还需要*单独声明 <\T>*。
4. *任何使用到泛型的地方，都应该指明 泛型的实际使用类型*。
```java
// 在类名后加 <T> 声明：我这个类使用了泛型，名字为 T
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
	public static <T> JsonResult<T> success() {    // 静态方法使用泛型 单独声明
	    return success(null);  
	  
	}  
	public static <T> JsonResult<T> success(T data){   // 静态方法使用泛型  单独声明 
	    JsonResult jsonResult = new JsonResult();  
	    jsonResult.setState(ServiceCode.OK.getValue());  
	    jsonResult.setData(data);  
	  
	    return jsonResult;  
	}
}
```

4. 之后在 *Controller*类 里的方法上也写上泛型
```java
public JsonResult<List<AlbumListVO>> list() {
	// ...
}
```

- 当使用涉及泛型的方法和类型时，如果你**不关心**泛型的*实际使用类型*，则应该通过`Void`类型来表示，例如：
```java
public JsonResult<Void> delete() {
	// ...
}
```