


### Java内存分配

**1. 栈内存**：*方法* 运行时使用的内存，比如main方法运行，进入方法栈中执行（执行时进栈，执行完毕出栈）。
   - *局部变量*：存储正在调用的方法中的局部变量（包括方法的参数）。

**2. 堆内存**：存储 *对象* ，*实例变量(成员变量)* 或着 *数组*，*new出来创建的*，都存储在堆内存。
   - *StringTable(串池)*：存储直接赋值的字符串对象。
   - *静态存储位置(静态区)*：存储被 static 修饰的静态变量。

**3. 方法区**：一个类要被使用时，这个类的 *.class* 字节码文件就要加载到方法区临时存储。
   - *属于类的，存储在方法区中，只有一份* 。

**4. 本地方法区**：JVM在使用操作系统功能的时候使用，与我们开发无关。

**5. 寄存器**：给CPU使用，和我们开发无关。






### 驼峰命名

**1. 小驼峰命名法：方法、变量**
   - 标识符是一个单词时，全部小写。
     - 例如：name

   - 标识符由多个单词组成时，第一个单词首字母小写，其他单词首字母大写
      - 例如：firstName  

**2. 大驼峰命名法：类名**
   - 不论是一个单词还是多个单词，每个单词首字母都要大写
      - 例如：Student、GoodStudent



### 数组，成员变量 默认的初始化值规律

**1. 整数类型**：**0

**2. 小数类型**：0.0

**3. 字符类型**：' \u0000 ' （空格）

**4. 布尔类型**：fal

**5. 引用类型**：null



### 链式编程

**当我们在调用一个方法的时候，不需要用变量接收他的结果，可以直接继续调用其他方法**

```java
  int length = getString().substring(1).replace("a","q").length();
  System.out.println(length);

  public static String getString(){
	  Scanner sc = newScanner(System.in);
	  System.out.println("请输入一个字符串");
	  String s = sc.next();
	  return s;
  }
```



### 调用方法的规则

1. 同类的方法：直接调用

2. 不同类方法：
   - 实例方法：对象 . 方法
   - 静态方法：类名 . 方法



### 被 public 修饰的类要与类名保持一致



### 四种访问权限

|    修饰符     | 同一个类 | 同一个包 | 子类 | 任何地方 |
|:-------------:|:--------:|:--------:|:----:|:--------:|
|    public     |    √     |    √     |  √   |    √     |
|    protect    |    √     |    √     |  √   |          |
| 默认(default) |    √     |    √     |      |          |
|    private    |    √     |          |      |          |

   - 访问权限由低到高依次为：public > protected > 默认 > private

   - 类的访问权限，只能是 public或默认，成员变量4种都可以

   - 一般情况下：数据(成员变量)私有化(private)，行为(成员方法)大部分公开化(public)


### package  声明包

- 类的全称：包名.类名

- 同包中的类不能同名，不同包可以重名

- 作用：避免类的命名冲突

- 建议包名所有字母都小写，并常常带有层次( a.b.c )



### import 导入类

- 同包中的类可以直接访问，但不同包中的类不能直接访问，若想访问：
  - 先 import 导入类再访问类 ( import 类的全称 或 import 包.*   (导入这个包中的全部类) )



### 基本数据类型对应的包装类

- 包装类是引用数据类型

- 全部都首字母大写，char 和 int 特殊记

- 目的就是为了解决基本类型不能直接参与面向对象开发的问题

|   原    |    包装类     |
|:-------:|:-------------:|
|  byte   |     Byte      |
|  short  |     Short     |
|  char   | **Character** |
|   int   |  **Integer**  |
|  long   |     Long      |
|  float  |     Float     |
| double  |    Double     |
| boolean |    Boolean    |
   - 包装类都继承于Object
     - Character 和 Boolean 直接继承于 Object类
     - 其他都继承于 java.lang.Number 类

- **注意：**
- Integer.valueOf ( ) 会*复用* -128到127 范围内的数据
- *直接赋值触发自动装箱机制*，也会复用
- JDK1.5推出了一个新特性：自动拆装箱。当编译器编译时若发现是基本类型与包装类型之间的相互赋值，则自动补齐代码完成转换工作，这个过程叫做自动拆装箱。
     ```java
	 // 触发自动装箱的特性，会被编译成：Integer i = Integer.valueOf(5);
	 Integer i1 = 5;  
	 Integer i2 = 5;  
	 // 触发自动拆箱的特性，会被编译成：int j = i.intValue();
	 int j = i1;
	 
	 System.out.println(i1 == i2);  // true
	 System.out.println(i1.equals(i2));  //true
	   
	 Integer i3 = Integer.valueOf(5);  
	 Integer i4 = Integer.valueOf(5);  
	 System.out.println(i3 == i4);  //true
	 System.out.println(i3.equals(i4)) //true
	 
	 Integer i5 = Integer.valueOf(500);  
	 Integer i6 = Integer.valueOf(500);  
	 System.out.println(i5 == i6);  //false
	 System.out.println(i5.equals(i6)) //true
   ```

   - **包装类可以干什么：**
     - 可以通过包装类来获取基本数据类型的取值范围
       
     ```java
    int max = Integer.MAX_VALUE;     // 获取int的最大值
    int min = Integer.MIN_VALUE;     // 获取int的最小值
	long max1 = Long.MAX_VALUE;
	long min1 = Long.MIN_VALUE；
     ```

   - 包装类可以将字符串转换为对应的基本类型
     ```java
     String s1 = "38";
     int age = Integer.parsseInt(s1);   // 将字符串s1转换为int类型并赋值给age
     System.out.println(age);     // 38  int类型
     
     String s2 = "123.456";
     double price = Double.parseDouble(s2);     // 将字符串s2转换为double类
     System.out.println(price);     // 123.456  double类型
	   ```


### 加载 .class字节码文件

- 永远是先加载父类，再加载子类



## instanceof 关键字

- **判断 *这个变量名指向的对象* 是不是 *某个类型***

- **格式**：
  
   `变量名 instanceof 类型`
  
   返回  boolean  类型结果
   
   JDk14时出现的新特性：
     
     -  `变量名 instanceof 类型 目标变量名`
    
     - 先判断 *这个变量名所指向的对象* 是不是 *这个类型* ，
        如果是，*则强转成这个类型*，*转换后变量名*为 *目标变量名*。
        如果不是，则不强转，结果直接是false。
     
     - 相当于*把判断和强转和成一起写*

- 例如
  ```java
  if(a instanceof Dog){     // 判断 a指向的对象 是否为 Dog类型
	  Dog d = (Dog) a;
	  d.lookHome();
  }
  if(a instanceof Cat){     // 判断 a指向的对象 是否为 Cat类型
	  Cat c = (Cat) a;
	  c.catchMouse();
  }
  
  // JDK14时的新特性
  if(a instanceof Dog d){   // 是 Dog类型 则直接强转成Dog类型，转换后变量名为 d
	  d.lookHome();
  }
  
	```



## API 文档中的   E、 T

- 都是指代泛型