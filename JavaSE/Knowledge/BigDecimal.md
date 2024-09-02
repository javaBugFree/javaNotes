
BigDecimal创建后 **值 不可改变**，计算时也只是创建新的对象。


## 计算机中的小数(float / double) 

例如 *69.875* 转换为*二进制*为：
![[计算机中的小数：小数的二进制.png]]

不同的 *小数部分* 转换为二进制时，**占用的bit位** *也各不相同*。例如：
![[计算机中的小数：不同的小数部分占用的bit位也不同.png]]


而 *各浮点类型* 在将数据转换为二进制时，**最多占用的字节数** 为：
![[计算机中的小数：各浮点类型最多占用的字节数.png]]

所以，当需要转换的小数 *转换为二进制时*，如果 **超过了** 最多占用字节数，就会 *将后面的舍弃掉*，用这些数据去参与计算（加减乘除）的话，就会出现 *浮点偏移* 现象。


## BigDecimal 的 *作用*

而在实际开发当中例如 金融、证券、精密零件 相关的行业，需要*使用小数进行精确的运算*，为了解决这个问题，Java就提供了一个类：*BigDecimal*。

BigDecimal的 **作用**：
- 用于小数的 *精确运算*。
- 用来 *表示较大的小数*。

## *浮点偏移*

```java
// 浮点偏移  
double d1 = 0.1;  
double d2 = 0.2;  
System.out.println(d1 + d2);  
// 出现浮点偏移 0.30000000000000004
// 10/3   3.3333333333333333  
  
BigDecimal b1 = new BigDecimal(d1);  
BigDecimal b2 = new BigDecimal(d2);  
System.out.println(b1.add(b2));  
// 更精确：0.3000000000000000166533453693773481063544750213623046875  
  
BigDecimal b3 = new BigDecimal("0.1");  
BigDecimal b4 = new BigDecimal("0.2");  
System.out.println(b3.add(b4));  
// 如果都是字符串进行运算的话，会进行 对位计算： 0.3
```


## **构造方法**



**构造方法** 获取BigDecimal对象：
- `public BigDecimal(double val)`
- `public BigDecimal(String val)`

**静态方法** 获取BigDecimal对象：
- `public static BigDecimal ValueOf(double val)`


1. 通过传递 *double类型* 的小数来创建对象
    - *细节*：这种方式*有可能是不精确的*，所以 **不建议使用**。
    ```java
	BigDecimal bd1 = new BigDecimal(0.01);  
	BigDecimal bd2 = new BigDecimal(0.09);  
	  
	System.out.println(bd1);    // 不精确  
	System.out.println(bd2);    // 不精确
	```

2. 通过 **传递字符串** *表示的小数* 来 *创建对象*
    ```java
    BigDecimal bd3 = new BigDecimal("0.01");  
	BigDecimal bd4 = new BigDecimal("0.09");  
	// 两个BigDecimal相加  
	BigDecimal add = bd3.add(bd4);
	
	System.out.println(bd3);  // 0.01
	System.out.println(bd4);  // 0.09
	System.out.println("bd3 + bd4 = " + add);  // 0.10
	```

3. 通过 **静态方法** *获取对象*
    - *细节*：这种方式获取 *小数对象* 时，传递的val都会使用`Double.toString(val)`作为参数
    ```java
    BigDecimal bd6 = BigDecimal.valueOf(10);      
	System.out.println(bd6);    // 10.0
	```


*推荐的* 两种创建方式的 **细微差别**：
1. 如果要表示的 *数字不大*，**没有超出** *double的取值范围*，建议使用 **静态方法**。
2. 如果要表示的 *数字比较大*，**超出了** double的取值范围，建议使用 **构造方法**。
3. 如果我们传递的是 *0~10之间的 整数，包含 0和10*，那么 **静态方法** 会 *返回* **已经创建好的对象**，*不会重新new*。
    - 例如：
    ```java
    BigDecimal bd7 = BigDecimal.valueOf(10);
	BigDecimal bd8 = BigDecimal.valueOf(10);
	BigDecimal bd9 = BigDecimal.valueOf(10.0);  // 小数，都是重新new
	System.out.println(bd7 == bd8);     // true，两个方法返回的都是原先已经创建好的同一个对象
	System.out.println(bd7 == bd9);     // false
	```



## BIgDecimal的 **使用**

| 方法名                                                          | 说明     |
|:-------------------------------------------------------------  |:-------- |
| public static BigDecimal `valueOf`(double val)                 | 获取对象 |
| public Bigdecimal `add`(BigDecimal val)                        | 加法     |
| public BIgdecimal `subtract`(Bigdecimal val)                   | 减法     |
| public BIgdecimal `multiply`(Bigdecimal val)                   | 乘法     |
| public BIgdecimal `divide`(Bigdecimal val)                     | 除法     |
| public BIgdecimal `divide`(Bigdecimal val, 精确几位, 舍入模式)   | 除法     |

1. **加法**
    ```java
    BigDecimal bd10 = BigDecimal.valueOf(10.0);  
	BigDecimal bd11 = BigDecimal.valueOf(2.0);  
	  
	BigDecimal add1 = bd10.add(bd11);  
	System.out.println("add: " + add1);    // 12.0
	```

2. **减法**
    ```java
    BigDecimal subtract = bd10.subtract(bd11);  
	System.out.println("subtract: " + subtract);    // 8.0
	```

3. **乘法**
    ```java
    BigDecimal multiply = bd10.multiply(bd11);  
	System.out.println("multiply: " + multiply);    // 20.00
	```

4. **除法**
    - 分为 *除的尽* 和 *除不尽* 两种情况
    ```java
    // 除的尽时：
    BigDecimal divide1 = bd10.divide(bd11);     // 除的尽时，可以直接除  
	System.out.println("divide1: " + divide1);  // 5  
	
	// 除不尽时：
	BigDecimal bd12 = BigDecimal.valueOf(3.0);  
	// BigDecimal divide2 = bd10.divide(bd12);     // 除不尽时，直接除会报错  
	// System.out.println("divide2: " + divide2);  // 报错  
	
	// 设置 精度为：保留两位小数，舍入模式为：四舍五入
	BigDecimal divide3 = bd10.divide(bd12, 2, RoundingMode.HALF_UP);
	System.out.println("divide3: " + divide3);  // 不会报错，结果为：3.33
	```


## 舍入模式（RoundingMode）

| 舍入模式        | 介绍                                          |
|:--------------- |:--------------------------------------------- |
| **UP**          | *远离零方向* 舍入的舍入模式                   |
| **DOWN**        | *向零方向* 舍入的舍入模式                     |
| **CEILING**     | *向 正无限大 方向* 舍入的舍入模式             |
| **FLOOR**       | *向 负无限大 方向* 舍入的舍入模式             |
| **HALF_UP**     | *向最接近数字方向* 舍入的舍入模式（四舍五入） |
| **HALF_DOWN**   | *向最接近数字方向* 舍入的舍入模式             |
| **HALF_EVEN**   | *向最接近数字方向* 舍入的舍入模式             |
| **UNNECESSARY** | 用于断言请求的操作具有精确结果的舍入模式      |

1. **UP**
    - *远离零方向* 舍入的舍入模式。始终对非零舍弃部分前面的数字加 1。
    - 注意，此舍入模式始终不会减少计算值的绝对值。
    ![[舍入模式：UP.png]]

2. **DOWN**
    - *向零方向* 舍入的舍入模式。从不对舍弃部分前面的数字加 1（即截去尾）。
    - 注意，此舍入模式始终不会增加计算值的绝对值。
    ![[舍入模式：DOWN.png]]

3. **CEILING**
    - *向正无限大方向* 舍入的舍入模式。
        - 如果 *结果为正*，则舍入行为类似于 RoundingMode.*UP*；
        - 如果 *结果为负*，则舍入行为类似于 RoundingMode.*DOWN*。
    - 注意，此舍入模式始终不会减少计算值。
    ![[舍入模式：CEILING.png]]

4. **FLOOR**
    - *向负无限大方向* 舍入的舍入模式。
        - 如果 *结果为正*，则舍入行为类似于 RoundingMode.*DOWN*；
        - 如果 *结果为负*，则舍入行为类似于 RoundingMode.*UP*。
    - 注意，此舍入模式始终不会增加计算值。
    ![[舍入模式：FLOOR.png]]

5. **HALF_UP**
    - 即：*四舍五入*。
    - 向最接近数字方向舍入的舍入模式，如果 *与两个相邻数字* 的 *距离相等*，则 *向上舍入*。
        - 如果被舍弃部分（小数点后的数字） *>= 0.5*，则舍入行为同 RoundingMode.*UP*；
        - 否则舍入行为同 RoundingMode.DOWN。
    ![[舍入模式：HALF_UP（四舍五入）.png]]

6. **HALF_DOWN**
    - 向最接近数字方向舍入的舍入模式，如果 *与两个相邻数字* 的 *距离相等*，则 **向下舍入**。
        - 如果被舍弃部分（小数点后的数字） *> 0.5*，则舍入行为同 RoundingMode.*UP*；
        - 否则舍入行为同 RoundingMode.DOWN。
    ![[舍入模式：HALF_DOWN.png]]

7. **HALF_EVEN**
    - 向最接近数字方向舍入的舍入模式，如果 *与两个相邻数字* 的 *距离相等*，则 **向相邻的偶数舍入**。
        - 如果 *舍弃部分左边的数字（小数点前）* 为 *奇数*，则舍入行为同 RoundingMode.*HALF_UP*；
        - 如果为 *偶数*，则舍入行为同 RoundingMode.*HALF_DOWN*。
    - 注意，在重复进行一系列计算时，此舍入模式可以在统计上将累加错误减到最小
    - 。此舍入模式也称为“银行家舍入法”，主要在美国使用。此舍入模式类似于 Java 中对 float 和 double 算法使用的舍入策略。
    ![[舍入模式：HALF_EVEN.png]]

8. **UNNECESSARY**
    - *用于 断言请求的操作 具有精确结果* 的舍入模式，因此不需要舍入。如果对生成精确结果的操作指定此舍入模式，则抛出 ArithmeticException。
    ![[舍入模式：UNNECESSARY.png]]



## BigDecimal的 *底层存储方式*

将字符串 *遍历得到每一个字符*，然后转换为 *ASCII表* 中的 *对应的数值*，再来使用 *数组* 进行存储。

![[BigDecimal的底层存储方式1.png]]

例如：
![[BigDecimal的底层存储方式2.png]]

