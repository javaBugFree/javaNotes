
- Object中有两个经常被派生类重写的方法：toString()和equals()


**1. 重写toString()**

- Object的 toString()默认返回  类的全称( 包名.类名 )@地址
- 所以需要重写来输出想要输出的内容
  > 注意：
  > 	String、StringBuilder、Collection等都重写了toString()来返回字符串内容了


**2. 重写 equals ( )**

- equals ( ) 方法的内部：
 ```java
     public boolean equals(Object obj) {  
	    return (this == obj);  
	}
```
  
   - 调用Object中的equals()，内部默认还是比较地址值，没有参考意义
   - 若想比较对象的属性值是否相同，我们认为 Object 的 equals() 并不能满足需求
   - 因此常常需要重写equals()
   > 注意：
   > 1. String类和ArryList类已经重写了equals()，但 StringBuilder类 并没有
   > 	Integer类也默认重写了
   > 2. 重写equals()的基本原则：
   > 		原则上要比较两个对象的属性值是否相同
   > 		两个对象必须是同一类型的，若类型不同则返回false

```java
    public class Point{
		private int x;  
		private int y;
		
		// 重写equals方法
		@Override  
		public boolean equals(Object o) {  
		    if (this == o) return true;     // 如果地址相同，直接返回true  
		    if (o == null || getClass() != o.getClass()) return false;  
		    Point point = (Point) o;  
		    return x == point.x && y == point.y;  
		}  
		
		@Override  
		public int hashCode() {  
		    return Objects.hash(x, y);
		}   
	}
```


   - String类和ArryList类已经重写了equals()，但 StringBuilder类 并没有
   ```java
	String s1 = new String("123");  
	String s2 = new String("123");  
	System.out.println(s1.equals(s2));  // true    已经默认重写了
	
	StringBuilder sb1 = new StringBuilder("123456");  
	StringBuilder sb2 = new StringBuilder("123456");  
	System.out.println(sb1.equals(sb2));    // false   需要重写equals()
```


   - Integer类也默认重写了