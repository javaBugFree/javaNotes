
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace 的值设定为被映射的包名.类名 -->
<mapper namespace="com.example.xmldemo.mapper.ProductMapper">
    <!-- select 元素用于映射查询语句到方法，
         id值必须和mapper接口中的方法名一致！
         需要设定返回值类型resultType，类型与方法返回值类型一致
         select 元素中定义SQL，SQL查询结果将自动映射到方法返回值，
         不要使用分号结尾！！-->
    <select id="countProduct" resultType="java.lang.Integer">
        SELECT COUNT(*) from product
    </select>
</mapper>
```

- **XML**：*可扩展的标记语言*：
    - 标签可以*任意名称*，标签名可以扩展
    - 标签*嵌套关系*可以扩展，标签可以*任意嵌套*
    - *属性*可以扩展

- **处理节点**：`<?xml version="1.0" encoding="UTF-8" ?>`
    - 从来没有变过！可以省略，但不建议省略！

- **文档定义**：`DOCTYPE` 用于约定XML文件中的 元素、属性、嵌套关系
    - 可以约束标签和属性

- **标签/标记**：`<mapper>`
    - *必须 成对使用*，有开始标签就必须有结束标签：`<mapper></mapper>`

- **开始标签**上可以*定义属性*：`id="countProduct"`
    - *属性名 不可以重复*，属性*无顺序*

- XML文件只能有*唯一的根元素*！！！

- XML中，*大小写敏感*，不同！

