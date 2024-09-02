
MyBatis Mapper 接口的 **方法作为id**，*不能重复*
所以就说明：*方法不能重载*

## 如何处理带参查询

### 处理一个参数查询

例子：
```sql  
SELECT count(*) FROM product WHERE price > ?  
```

![[处理一个参数的查询.png]]

只有一个参数的话，WHERE子句中判断语句名字 **可以** *不一致*，例如：
```sql
<select id = "countByprice" resultType = "Integer">
	SELECT count(*) 
	FROM product 
	WHERE price > #{123price}
</select>
```


### 处理多个参数查询

Java编译问题：*方法参数为多个时，方法编译后* **参数名可能会消失**，只会被记录成第一个参数，第二个参数...。
所以需要 *给参数加 @Param注解*

多参数查询的时候，建议*使用 @Param("price") 为参数定义 占位符名称*

如果使用的是SpringBoot官方创建的脚手架，可以不加该注解，因为SpringBoot官方的脚手架设置的不会让参数名消失，但**不推荐**！

![[处理多个参数的查询.png]]


## 如何处理实体类型返回值

使用 Product 实体类作为返回值，在 **resultType上** *指定实体类型* 就可以了。

例如：`resultType="com.example.xmldemo.entity.Product"`

- 当 *实体类的属性名* 与 *数据库中列的名字* **不同**时，直接查询并返回结果集赋值给实体类 是*不会*给不同的那个属性名*赋值*的。
    - 解决方法，给数据库中名字不同的那个*列* **起个别名**，让 *别名 与 实体类那个属性名 一致*也可以赋值。


![[返回值为实体类型时.png]]


## 如何处理实体类型的集合

返回一组实体对象，必须有 **resultType** *值是 返回的 集合中的元素类型*。
例如：`com.example.xmldemo.entity.Product`


## **插入<\insert>**

插入数据SQL：
```sql
INSERT INTO product (id, title, price, num) VALUES(null, ?, ?, ?)
```

ProductMapper接口：**插入、更新、删除方法只有一个 默认返回int值，表示SQL影响行数**。
```java
Integer insertProduct(@Param("title") String title, 
			          @Param("price") Double price, 
			          @Param("quantity") Integer quantity);
```

在MyBatis ProductMapper.xml：
```xml
<!-- insert 插入语句不需要定义 resultType，默认就有int返回值 -->
<insert id="insertProduct" >
    INSERT INTO product (id, title, price, num) 
    VALUES(null, #{title}, #{price}, #{quantity})
</insert>
```




## ***利用 POJO对象 打包传递参数***

前面的都是使用**变量传递参数**。
*特点*：参数少，没有问题，但是参数多了以后就麻烦了，书写繁琐复杂

- **POJO** 是 *简单的 Java对象*，是 **entity实体类**、**值对象VO**、**数据传输对象DTO** 的总称
    
    - **entity 实体类**：通常 ***实体类的属性*** 和 ***表字段*** 是 ***一一对应*** 的。实体对象Product对象就是实体类。
        - 用到*一个表*，就创建*一个对应的实体类*.
    
    - **VO（ValueObject 值对象）**：从***数据库里面 查询出来的数据***，通过 *VO* 对其进行 *封装*。
        - *查到什么 字段*，就在VO 里面*定义什么 属性*。
        - 查询出来的 *不同的 字段结果集*，使用 *不同的 VO对象封装*。
        - 会*混淆null值*：查了此字段，但没有值，所以是null 或 根本没有查这个字段，所以是null。
    
    - **DTO（DataTransferObject 数据传输对象）**：*客户端给服务器* ***传输 多个参数*** 时，通过*DTO*进行*封装*。
        - *客户端 传给服务器什么*，就在DTO对象类中 *定义什么属性*。
        - 和entity实体类的**区别**
            - *实体类*里属性的数量是*和表一致*的，而*DTO类*里的属性数量是*和客户端 传给服务端的一致*
            
        - 使用DTO类，就*不会出现*多余的属性是*空值*。可以通过*输出DTO类* **查看属性是否为NULL** 来*判断*，**客户端有没有准确传过来参数**。 


使用POJO对象作为Mapper方法参数：ProductMapper，*无需定义@Param*
```java
Integer saveProductItem(Product product);

// 如果是POST请求，则需要加上 @RequestBody注解
Integer saveProductItem(@RequestBody Product product);
```

Mybatis 自动将 **POJO对象的属性** *映射传递到* **#{占位符}**
```xml
<!-- Mybatis 会自动的 将 product 的属性 映射到#{title}, #{price}, #{quantity}
     要求 #{title}, #{price}, #{quantity} 占位符必须和 product 的属性（getXxx）一致-->
<insert id="saveProductItem">
    INSERT INTO product (id, title, price, num )
    VALUES (null, #{title}, #{price}, #{quantity})
</insert>
```

测试
```java
@Test  
void saveProductItem(){  
    Product product = new Product(null, "大力手机", 2000.0, 100);
    Integer n = productMapper.saveProductItem(product);
    System.out.println(n);
}
```

![[利用POJO对象打包传递参数.png]]


## **返回自动增加的ID**

**使用POJO作为参数** 插入数据 的时候，可以返回自增的ID：
- *useGeneratedKeys="true"*：使用生成的key。
- *keyProperty="id"*：key的属性名。
```xml
<!-- useGeneratedKeys="true"：使用生成的key。
     keyProperty="id"：key的属性名。-->
<insert id="saveProductItem" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO product (id, title, price, num)
    VALUES (null, #{title}, #{price}, #{quantity})
</insert>
```

## **更新数据<\update>**

更新数据 SQL，更新一行的全部数据：
```sql
UPDATE product
SET title = ?, price = ?, num = ?
WHERE id = ?
```

ProductMapper接口
```java
Integer updateProduct(Product product);
```

ProductMapper.xml
```xml
<update id="updateProduct">
UPDATE product
SET title = #{title}, price = #{price}, num = #{quantity}
WHERE id = #{id}
<\update>
```

## **Mabatis 动态SQL拼接**

根据*参数的条件*，**动态生成SQL语句**，*提升SQL的效率*。

- 动态SQL标签
    - if：
    - choose：
    - when：
    - foreach：
    - 等...

### 动态SQL,更新部分数据

ProductMapper接口
```java
Integer updateProductPart(Product product);
```

ProductMapper.xml文件
```xml
<!--检查参数，动态拼接SQl-->
<!--test="title is null"：检查title参数是否为空-->
<!--test="title != null"：检查title参数是否不为空，拼接一段SQL title=#{title},-->
<!--SET变成<set>会自动帮我们删除多余的 "," 否则SET没有最后一个字段的话会报错-->
<update id="updateProductPart">
    UPDATE product
    <set>
        <if test="title != null">
            title=#{title},
        </if>
        <if test="price != null">
            price=#{price},
        </if>
        <if test="quantity != null">
            num=#{quantity}
        </if>
    </set>
    WHERE id = #{id}
</update>
```

test
```java
/*  
    动态拼接SQL
    只更新部分属性
*/  
@Test  
void updateProductPart(){
	
    Product p2 = new Product(23,null,1000.0 ,23);  
    Integer i = productMapper.updateProductPart(product);  
    System.out.println(product);  
    System.out.println(i);  
}
```


### 动态SQL,批量删除

#### 第一种方法：根据 **id数组** 批量删除

根据 *id数组* 批量删除

ProductMapper接口
```java
/*
	根据 商品id数组 批量删除
*/
Integer deleteByIdArr(Integer[] idArr);
```

ProductMapper.xml文件
```xml
<!--
	根据 商品id数组 批量删除
    <foreach>           循环遍历标签
    collection="array"  声明要遍历的是一个数组
    item="id"           设置遍历过程中的变量名，表示 删除时用的数组里的元素
    separator=","       设置分隔符为 ","
    open="("            循环开始时添加的内容
    close=")"           循环结束时添加的内容，这两个组合使用可以代替 IN 后面的 "()"
-->
<delete id="deleteByIdArr">
	DELETE FROM product
	WHERE id IN (
	<foreach collection="array" item="id" separator=",">
	    #{id}
	</foreach>
	)
</delete>
```

Test
```java
@Test
void deleteByIdArr(){
    // 准备一个id数组
    Integer[] idArr = {37,38};
    // 调用Mapper中的删除方法实现删除
    Integer i = productMapper.deleteByIdArr(idArr);
    System.out.println("影响行数：" + i);
}
```


#### 第二种方法：根据 **变长参数** 批量删除

根据 *变长参数* 批量删除

productMapper接口
```java
/*
    根据 变长参数 批量删除
    方法接收一个变长参数
*/
Integer deleteByParam(Integer... id);
```

productMapper.xml文件
```xml
<!--根据 变长参数 批量删除
    方法参数是变长参数，编译的时候其实就是 一个数组-->
<delete id="deleteByParam">
    DELETE FROM product
    WHERE id IN (
    <foreach collection="array" item="id" separator=",">
        #{id}
    </foreach>
    )
</delete>
```

Test
```java
@Test  
void deleteByParam() {  
    // 传递变长参数，商品的id  
    Integer i = productMapper.deleteByParam(39, 40);  
    System.out.println("影响行数：" + i);  
}
```


#### 第三种方法：根据 **商品id集合** 批量删除

根据 *商品id集合* 批量删除

productMapper接口
```java
/*
    根据 商品id集合 删除
*/
Integer deleteByIdList(List<Integer> id);
```

productMapper.xml文件
```xml
<!--根据 商品id集合 批量删除
    collection="list"  声明要遍历的是一个集合-->
<delete id="deleteByIdList">
    DELETE FROM product
    WHERE id IN (
    <foreach collection="list" item="id" separator=",">
        #{id}
    </foreach>
    )
</delete>
```

Test
```java
@Test
void deleteByIdList(){
    // 准备一个集合
    List<Integer> list = new ArrayList<>();
    list.add(41);
    list.add(42);
    // 调用方法
    Integer i = productMapper.deleteByIdList(list);
    System.out.println("影响行数：" + i);
}
```


## SQL语句的 复用

当 *多个XML标签* 中的 *SQL语句有重复代码*时，可以使用 **XML** 的 **<\sql>标签** 和 **<\include>标签** 进行*复用*。

- **<\include> 标签**：对 *<\sql>标签* 中的*SQL语句*进行*复用*。
    - *refid 属性*：*定位*<\sql>标签的*id*。
    - 通常，在`<include>`标签的子级并不编写任何代码，所以，此标签推荐使用*单标签*格式。
        - 例：`<include refid="query"/>`

- **<\sql> 标签**：使用该标签进行*SQL语句 的 复用*。
    - *id 属性*：*声明*该<\sql>标签的*id*。

- 由于 IntelliJ IDEA会检查每个标签的子级内容的语法，所以在 `<sql>` 标签下直接写字段列表，IntelliJ IDEA会提示错误，但是并不影响运行

```xml
<!-- SQL语句的 复用 -->
<!-- <include refid="query"></include>  复用<sql>标签中的SQL语句  
     refid  定位复用的<sql>标签的id -->
<select id="select" resultType="com.example.xmldemo.entity.Product">
    <include refid="query"/>
</select>
<select id="selectById" resultType="com.example.xmldemo.entity.Product">
    <include refid="query"></include>
    WHERE id=#{id}
</select>
<!-- 上述两个查询sql中有重复代码
     SELECT id, title, price, num AS quantity
     FROM product -->

<!-- 使用<sql>标签进行 复用
     id  声明该<sql>标签的 id -->
<sql id="query">
    SELECT id, title, price, num AS quantity
    FROM product
</sql>
```