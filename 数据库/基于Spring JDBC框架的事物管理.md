
事物（Transaction）：是*数据库*（例如MySQL等）中能够保障 **若干个** *写操作*（增、删、改）*要么全部成功*，*要么全部失败*的机制。
例如：同一个方法中，数据库 增操作 完成，中间失败了，后面 改操作 没执行，就全部判为失败。只有全部都成功才成功！

**通过AOP来实现的。**

对于数据的管理，无论是全部成功，或全部失败，其实都是可以接受的结果，而只成功一半，是不可接受的！
例如：存在某个转账操作：
```sql
update 存款表 set 余额 = 余额-10000 where 账户 = "张三";
```
*中间操作*。如果失败，则李四收不到转账，张三亏炸。只有全部成功或全部失败才能接受！
```sql
update 存款表 set 余额 = 余额+10000 where 账户 = "李四";
```

<br>

在基于Spring JDBC框架的事物管理中，**方法上**使用 `@Transactional` 注解，即可使得对应的方法是 “事务性*” 的。
*提示*：间接引用该框架，并*不需要直接导入相关依赖*，Mybatis的依赖里面带有相关的。
即，使用`@Transactional`注解实现事物，被称之为“声明式事务“。
*提示*：主流的基于Spring体系的编程框架，在处理数据库编程时，都使用到了SpringJDBC。

> 提示：Spring JDBC框架在实现事务管理时，使用了*基于接口*的*代理模式*！（不带普通方法玩，哪怕定义在接口）

- 关于此注解，**可以添加在**：
    - **业务接口**上
        - 将作用于 *实现此接口的 实现类* 中的 *所有 重写的方法*上。
        - 缺点：如果 实现类的重写的方法 *只有一个操作*，也会被变成事务性，会造成*浪费*。
    - **业务接口中的 抽象方法**上（建议！按需添加）
        - 将作用于 实现此接口的 *实现类* 中 *重写的 对应的方法*上。
    
    - **业务实现类**上
        - 将作用于 *此实现类* 的 *所有 重写的方法*上。
    - **业务实现类中的 重写的方法** 上
        - 将作用于 *当前方法*。

在应用时，仅当某个业务方法中包含*超过1次的写操作*（例如2次UPDATE，或2次INSERT加上1次DELETE等），*才必须需要* 保障此业务时事务性的！反之，如果某个业务中的写操作不超过1次，可以不必是事务性的！

在Spring JDBC框架中，对 *事务的处理* **大致流程**是：
```
try{
	开启事物：BEGIN
	<执行业务方法>
	提交事物：COMMIT
} catch (RuntimeException e) {
	回滚事物：ROLLBACK
}
```
也就是说，在基于Spring JDBC的事物管理中，**仅当出现了**`RuntimeException` *或其子孙类异常*，才会执行回滚。所以，在当**执行写操作后**，*应该获取 返回的* *受影响的行数*，并*判断是否为预期值*，**如果不是**，必须抛出 `RuntimeException` 或其子孙类异常，以使得事物回滚！

- 在`@Transactional`注解中，哪些异常的 回滚或不会滚：
    - 可以通过`rollbackFor = ** , **`*属性* 或 `rollbackForClassName`来 **指定** *出现哪些异常时才 回滚*！
        例如：
        ```java
        @Transactional(rollbackFor = {  
		    ServiceException.class,
		    NullPointerException.class  
		})
		
		// 或
		@Transactional(rollbackForClassName = {  
		   "cn.tedu.csmall.product.ex.ServiceException",
		   "java.lang.NullPointerException"  
		})
		```
    - 还可以通过`noRollbackFor`属性或 `noRollbackForClassName`，来 **指定** 对于 *哪些异常 不执行回滚*！
        例如：
        ```java
        @Transactional(noRollbackFor = {
		    ClassCastException.class,
		    IndexOutOfBoundsException.class  
		})
		```
    - 以上四个属性都是*数组类型*。
    - **注意：** 无论如何配置，导致回滚的异常类型都必须是`RuntimeException`的*子孙类*。

- **小结**：
    - 如果某个业务方法中 包含 *超过一次的写操作*，*必须保障* 此业务是 *事务性*的。
    - 在业务方法中 调用Mapper的增、删、改操作后，**都应该**获取返回的“*受影响的行数*”，并*判断是否为预期值*，*如果不是*，必须抛出`RuntimeException`或其子孙类异常，触发回滚机制！

- 另外，推荐课后了解以下内容（面试题，背下来）：
    - 事务的四大特性（ACID）
	    - 原子性
	    - 一致性
	    - 隔离性
	    - 持久性（永久性）
    - 事务的传播
    - 事务的隔离