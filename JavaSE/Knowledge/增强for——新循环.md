
1. **JDK1.5 时推出的新特性：增强型for循环，也称为新循环，让我们使用相同的语法遍历集合和数组**


2. **语法：** `for(数组或集合中的元素的类型 变量名 : 数组或集合名){ 循环体; }`
   - *快速语法：* `数组和集合名 . for`
   - 例如：
    ```java
	// 遍历数组
	String[] arr = {"a","b","c","d","e"};  
	for (String s: arr) {  
	    System.out.println(s);  
	}
	
	// 遍历集合
	ArrayList<Integer> list = new ArrayList<>();  
	list.add(11);  
	list.add(22);  
	list.add(33);  
	for (int i : list) {  
	    System.out.println(i);  
	}
	```



- 遍历数组：for循环
- 遍历集合：迭代器：遍历集合 *过程中* **安全地移除** 集合中的元素

- 增强for / 新循环遍历集合：