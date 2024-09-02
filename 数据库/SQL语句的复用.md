
当 *多个XML标签* 中的 *SQL语句有重复代码*时，可以使用 **XML** 的 ***<\sql>标签*** 和 ***<\include>标签*** 进行*复用*。
<br>
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
