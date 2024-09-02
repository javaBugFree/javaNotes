
Mybatis自带 **2级** 缓存机制，即Mybatis在 **满足特定条件** 的情况下，会 **将查询结果保存起来**，如果下次需要查询的*与此前 保存的 结果是相同的*，则下次*并不会*真正的查询，而是*直接 将此前缓存的结果 返回*，以提高查询效率。


## 一级缓存

Mybatis的 **一级缓存** 是基于 *SqlSession* 的，**默认是开启的（不可控）**。基本上人为不可控，当使用*同一个SqlSession*，*同一个Mapper*，执行*同样的SQL*，并且SQL语句中需要*传入的参数是相同的*，则一级缓存生效。
例如：
```java
@Autowired  
SqlSessionFactory sqlSessionFactory;  
  
@Test  
void t1(){  
    SqlSession sqlSession = sqlSessionFactory.openSession();  
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);  
  
    System.out.println("准备查询ID=1的品牌...");  
    Object result = brandMapper.getStandardById(1L);  
    System.out.println("查询到ID=1的数据：" + result);  
  
    System.out.println("准备查询ID=1的品牌...");  
    Object result1 = brandMapper.getStandardById(1L);  
    System.out.println("查询到ID=1的数据：" + result1);  
}
```
结果：
![[一级缓存.png]]

- 一级缓存会 **在以下情况失效**：
    - *不满足使用条件*：不是同一个SqlSession，或不是同一个Mapper，或没有同样的SQL，或传入的参数是不同的。
    - *手动调用方法清除缓存*：调用 `SqlSession` 对象的 `clearCache()` 方法。
    - *当前Mapper执行了任何写操作*，即使这个写操作与当前需要查询的数据无关，甚至这个写操作执行完毕后受影响的行数为0，也会清除一级缓存数据。


## 二级缓存

Mybatis的二级缓存是基于`namespace`的，在Spring Boot项目中，**默认是全局开启**的（**可控**），但各`namespace`*并未开启*，二级缓存 **只需要** *同一个namespace*、*执行同样的SQL*、*传入同样的参数*，就是有效的！
  
如果是通过*XML文件*来配置SQL语句，需要在XML文件中添加`<cache/>`标签，以开启当前namespace的二级缓存！
![[二级缓存.png]]
  
当在namespace中开启二级缓存后，还可以在*各查询的`<select>`标签上*配置`useCache`属性，如果取值为`false`，将*对此查询不启用二级缓存*！
  
**注意**：启用二级缓存后，查询的*返回结果类型*必须*实现`Serializable`接口*！
  
二级缓存也会因为当前namespace中*执行了任何写操作而销毁*！


## 流程

Mybatis在执行查询时，会 **先** 从*二级缓存*中查找缓存数据，如果命中，则返回，如果未命中，则会 **再** 从*一级缓存*中查找缓存数据，如果命中，则返回，如果仍未命中，则会执行真正的查询。




















