
以上在 **application.properties** 中添加的是连接数据库的配置信息，当前处于*开发阶段*，所以，这个配置值都是开发时所使用的MySQL的相关配置，例如**localhost**、**3306**、**用户名**、**密码**等，
当*开发完成后*，可能会有专门的测试人员对此项目进行测试，测试时使用的MySQL肯定不会是当前开发使用的MySQL，则以上属性的值都需要修改，
当*测试通过后*，项目正式上线，使用的MySQL又会不同，则各属性值需要再次修改...
即使全部改完了，项目可能需要*进入下一阶段的开发*，以上各属性值又要改回开发时的配置值。
在开发实践中，除了连接数据库的配置信息以外，其他配置也可能会根据项目的运行环境不同，而使用不同的配置值！


*Spring Boot框架* 很好的支持了**Profile配置**的机制，*允许同时存在 若干个配置文件*，这些配置文件 **默认并不会激活**（*默认是无效的*），当 *需要使用哪套* 配置，就 *激活哪套* 配置文件即可。这样的配置文件称之为**Profile配置**。

*Spring Boot框架*很好的支持了Profile配置，其**规则**是：
1. **application.properties** 是*主配置文件*，其中的所有配置都是*默认加载*的。
2. 已**application-自定义名称.properties** 做为文件名的全部是Profile配置。*默认是不加载的*，*必须激活*，这些文件中的配置*才会被加载*。
3. 在 **application.properties（主配置文件）** 中，使用`spring.profiles.active`属性，可以激活Profile配置，
    - 此**属性的值**就是：Profile配置的*文件名*的*自定义部分*，
    - 如果有**多个Profile配置**需要激活，使用*逗号分隔*即可
    - 如果在**application.properties（主配置文件）** 中，与在Probablyfile配置中，*存在完全相同的属性的配置*，则*以Profile配置为准*。

例如：
![[Profile配置.png]]

以上图为例，可以在**application.properties**中配置`spring.profiles.active`属性，
- 当取值为`dev`时，**application-dev.properties**文件中的配置信息将被应用，
- 当取值为`test`时，**application-test.properties**文件中的配置信息将被应用，以此类推。  
  
在开发实践中，类似的配置文件可能较多，可以完全自行测试，
例如，可以创建**application-dev-jack.properties**、**application-dev-tom.properties**，就可以区分不同的开发人员，
当然，激活时，取值仍是自定义名称的部分，例如：`spring.profiles.active=dev-jack`。