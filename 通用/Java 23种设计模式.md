
1. ***单例设计模式***
   
   - 使用单例模式设计的类，*全局仅可以得到一个实例*。

   - **设计步骤**：
     1. *私有化构造器*     目的：封堵外界可以通过new随意实例化对象
     2. 定义 *静态的私有的* *当前类的属性* 并初始化(饿汉加载方式：我不管你用不用我先new好)  。
										       懒汉加载方式：你用的时候我再给你new
     3. 提供一个 *公开的静态的*  获取当前类实例的*方法*
	```java
	public class Singleton {  
		
		 // 2.定义 *静态的私有的* *当前类的属性* 并初始化
	     private static Singleton instance = new Singleton();  // 饿汉加载模式
	     
		 // 1.私有化构造器 
	     private Singleton(){}  
		 
		 // 3.提供一个 *公开的静态的* 获取当前类实例的*方法*
	     // 外界不能new了，我就定义一个方法给你一个实例  
	     public static Singleton getInstance(){  
	         return instance;  
	         // return new Singleton   懒汉加载模式
	     }  
	}
	
	public class SingletonTest {  
	    public static void main(String[] args) {  
	        Singleton s1 = Singleton.getInstance();  
	        Singleton s2 = Singleton.getInstance();  
	        // 两个对象是同一个对象，地址一样  
	        System.out.println(s1);  
	        System.out.println(s2);  
	    }  
	}
	```


2. 