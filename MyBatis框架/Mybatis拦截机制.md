
*csmall-product* 中为数据表添加 *创建时间、修改时间* 列 时用到了。

**注意**：由于*仅适用于当前项目*，并不具备范用性，所以
- 拦截所有的update方法（根据SQL语句以update前缀进行判定），无法不拦截某些update方法
- 所有数据表中"最后修改时间"的字段名必须一致，由本拦截器的FIELD_MODIFIED属性进行设置

1. 创建 *mybatis.InsertUpdateTimeInterceptor* 类
```java
/**  
 * <p>基于MyBatis的自动插入“创建时间”、更新"最后修改时间"的拦截器</p>  
 * 
 * <p>需要SQL语法预编译之前进行拦截，则拦截类型为StatementHandler，拦截方法是prepare</p>  
 * 
 * <p>具体的拦截处理由内部的intercept()方法实现</p>  
 * 
 * <p>注意：由于仅适用于当前项目，并不具备范用性，所以：</p>  
 * 
 * <ul>  
 * <li>拦截所有的update方法（根据SQL语句以update前缀进行判定），无法不拦截某些update方法</li>  
 * <li>所有数据表中"最后修改时间"的字段名必须一致，由本拦截器的FIELD_MODIFIED属性进行设置</li>  
 * </ul>  
 * 
 * @author java@tedu.cn 
 * @version 0.0.1 
 */
@Slf4j  
@Intercepts({@Signature(  
        type = StatementHandler.class,  
        method = "prepare",  
        args = {Connection.class, Integer.class}  
)})  
public class InsertUpdateTimeInterceptor implements Interceptor {  
  
    /**  
     * 自动添加的创建时间字段  
     */  
    private static final String FIELD_CREATE = "gmt_create";  
    /**  
     * 自动更新时间的字段  
     */  
    private static final String FIELD_MODIFIED = "gmt_modified";  
    /**  
     * SQL语句类型：其它（暂无实际用途）  
     */  
    private static final int SQL_TYPE_OTHER = 0;  
    /**  
     * SQL语句类型：INSERT  
     */    private static final int SQL_TYPE_INSERT = 1;  
    /**  
     * SQL语句类型：UPDATE  
     */    private static final int SQL_TYPE_UPDATE = 2;  
    /**  
     * 查找SQL类型的正则表达式：INSERT  
     */    private static final String SQL_TYPE_PATTERN_INSERT = "^insert\\s";  
    /**  
     * 查找SQL类型的正则表达式：UPDATE  
     */    private static final String SQL_TYPE_PATTERN_UPDATE = "^update\\s";  
    /**  
     * 查询SQL语句片段的正则表达式：gmt_modified片段  
     */  
    private static final String SQL_STATEMENT_PATTERN_MODIFIED = ",\\s*" + FIELD_MODIFIED + "\\s*=";  
    /**  
     * 查询SQL语句片段的正则表达式：gmt_create片段  
     */  
    private static final String SQL_STATEMENT_PATTERN_CREATE = ",\\s*" + FIELD_CREATE + "\\s*[,)]?";  
    /**  
     * 查询SQL语句片段的正则表达式：WHERE子句  
     */  
    private static final String SQL_STATEMENT_PATTERN_WHERE = "\\s+where\\s+";  
    /**  
     * 查询SQL语句片段的正则表达式：VALUES子句  
     */  
    private static final String SQL_STATEMENT_PATTERN_VALUES = "\\)\\s*values?\\s*\\(";  
  
    @Override  
    public Object intercept(Invocation invocation) throws Throwable {  
        // 日志  
        log.debug("准备拦截SQL语句……");  
        // 获取boundSql，即：封装了即将执行的SQL语句及相关数据的对象  
        BoundSql boundSql = getBoundSql(invocation);  
        // 从boundSql中获取SQL语句  
        String sql = getSql(boundSql);  
        // 日志  
        log.debug("原SQL语句：{}", sql);  
        // 准备新SQL语句  
        String newSql = null;  
        // 判断原SQL类型  
        switch (getOriginalSqlType(sql)) {  
            case SQL_TYPE_INSERT:  
                // 日志  
                log.debug("原SQL语句是【INSERT】语句，准备补充更新时间……");  
                // 准备新SQL语句  
                newSql = appendCreateTimeField(sql, LocalDateTime.now());  
                break;  
            case SQL_TYPE_UPDATE:  
                // 日志  
                log.debug("原SQL语句是【UPDATE】语句，准备补充更新时间……");  
                // 准备新SQL语句  
                newSql = appendModifiedTimeField(sql, LocalDateTime.now());  
                break;  
        }  
        // 应用新SQL  
        if (newSql != null) {  
            // 日志  
            log.debug("新SQL语句：{}", newSql);  
            reflectAttributeValue(boundSql, "sql", newSql);  
        }  
  
        // 执行调用，即拦截器放行，执行后续部分  
        return invocation.proceed();  
    }  
  
    public String appendModifiedTimeField(String sqlStatement, LocalDateTime dateTime) {  
        Pattern gmtPattern = Pattern.compile(SQL_STATEMENT_PATTERN_MODIFIED, Pattern.CASE_INSENSITIVE);  
        if (gmtPattern.matcher(sqlStatement).find()) {  
            log.debug("原SQL语句中已经包含gmt_modified，将不补充添加时间字段");  
            return null;  
        }  
        StringBuilder sql = new StringBuilder(sqlStatement);  
        Pattern whereClausePattern = Pattern.compile(SQL_STATEMENT_PATTERN_WHERE, Pattern.CASE_INSENSITIVE);  
        Matcher whereClauseMatcher = whereClausePattern.matcher(sql);  
        // 查找 where 子句的位置  
        if (whereClauseMatcher.find()) {  
            int start = whereClauseMatcher.start();  
            int end = whereClauseMatcher.end();  
            String clause = whereClauseMatcher.group();  
            log.debug("在原SQL语句 {} 到 {} 找到 {}", start, end, clause);  
            String newSetClause = ", " + FIELD_MODIFIED + "='" + dateTime + "'";  
            sql.insert(start, newSetClause);  
            log.debug("在原SQL语句 {} 插入 {}", start, newSetClause);  
            log.debug("生成SQL: {}", sql);  
            return sql.toString();  
        }  
        return null;  
    }  
  
    public String appendCreateTimeField(String sqlStatement, LocalDateTime dateTime) {  
        // 如果 SQL 中已经包含 gmt_create 就不在添加这两个字段了  
        Pattern gmtPattern = Pattern.compile(SQL_STATEMENT_PATTERN_CREATE, Pattern.CASE_INSENSITIVE);  
        if (gmtPattern.matcher(sqlStatement).find()) {  
            log.debug("已经包含 gmt_create 不再添加 时间字段");  
            return null;  
        }  
        // INSERT into table (xx, xx, xx ) values (?,?,?)  
        // 查找 ) values ( 的位置  
        StringBuilder sql = new StringBuilder(sqlStatement);  
        Pattern valuesClausePattern = Pattern.compile(SQL_STATEMENT_PATTERN_VALUES, Pattern.CASE_INSENSITIVE);  
        Matcher valuesClauseMatcher = valuesClausePattern.matcher(sql);  
        // 查找 ") values " 的位置  
        if (valuesClauseMatcher.find()) {  
            int start = valuesClauseMatcher.start();  
            int end = valuesClauseMatcher.end();  
            String str = valuesClauseMatcher.group();  
            log.debug("找到value字符串：{} 的位置 {}, {}", str, start, end);  
            // 插入字段列表  
            String fieldNames = ", " + FIELD_CREATE + ", " + FIELD_MODIFIED;  
            sql.insert(start, fieldNames);  
            log.debug("插入字段列表{}", fieldNames);  
            // 定义查找参数值位置的 正则表达 “)”            Pattern paramPositionPattern = Pattern.compile("\\)");  
            Matcher paramPositionMatcher = paramPositionPattern.matcher(sql);  
            // 从 ) values ( 的后面位置 end 开始查找 结束括号的位置  
            String param = ", '" + dateTime + "', '" + dateTime + "'";  
            int position = end + fieldNames.length();  
            while (paramPositionMatcher.find(position)) {  
                start = paramPositionMatcher.start();  
                end = paramPositionMatcher.end();  
                str = paramPositionMatcher.group();  
                log.debug("找到参数值插入位置 {}, {}, {}", str, start, end);  
                sql.insert(start, param);  
                log.debug("在 {} 插入参数值 {}", start, param);  
                position = end + param.length();  
            }  
            if (position == end) {  
                log.warn("没有找到插入数据的位置！");  
                return null;  
            }  
        } else {  
            log.warn("没有找到 ) values (");  
            return null;  
        }  
        log.debug("生成SQL: {}", sql);  
        return sql.toString();  
    }  
  
    @Override  
    public Object plugin(Object target) {  
        // 本方法的代码是相对固定的  
        if (target instanceof StatementHandler) {  
            return Plugin.wrap(target, this);  
        } else {  
            return target;  
        }  
    }  
  
    @Override  
    public void setProperties(Properties properties) {  
        // 无须执行操作  
    }  
  
    /**  
     * <p>获取BoundSql对象，此部分代码相对固定</p>  
     *     * <p>注意：根据拦截类型不同，获取BoundSql的步骤并不相同，此处并未穷举所有方式！</p>  
     *     * @param invocation 调用对象  
     * @return 绑定SQL的对象  
     */  
    private BoundSql getBoundSql(Invocation invocation) {  
        Object invocationTarget = invocation.getTarget();  
        if (invocationTarget instanceof StatementHandler) {  
            StatementHandler statementHandler = (StatementHandler) invocationTarget;  
            return statementHandler.getBoundSql();  
        } else {  
            throw new RuntimeException("获取StatementHandler失败！请检查拦截器配置！");  
        }  
    }  
  
    /**  
     * 从BoundSql对象中获取SQL语句  
     *  
     * @param boundSql BoundSql对象  
     * @return 将BoundSql对象中封装的SQL语句进行转换小写、去除多余空白后的SQL语句  
     */  
    private String getSql(BoundSql boundSql) {  
        return boundSql.getSql().toLowerCase().replaceAll("\\s+", " ").trim();  
    }  
  
    /**  
     * <p>通过反射，设置某个对象的某个属性的值</p>  
     *     * @param object         需要设置值的对象  
     * @param attributeName  需要设置值的属性名称  
     * @param attributeValue 新的值  
     * @throws NoSuchFieldException   无此字段异常  
     * @throws IllegalAccessException 非法访问异常  
     */  
    private void reflectAttributeValue(Object object, String attributeName, String attributeValue) throws NoSuchFieldException, IllegalAccessException {  
        Field field = object.getClass().getDeclaredField(attributeName);  
        field.setAccessible(true);  
        field.set(object, attributeValue);  
    }  
  
    /**  
     * 获取原SQL语句类型  
     *  
     * @param sql 原SQL语句  
     * @return SQL语句类型  
     */  
    private int getOriginalSqlType(String sql) {  
        Pattern pattern;  
        pattern = Pattern.compile(SQL_TYPE_PATTERN_INSERT, Pattern.CASE_INSENSITIVE);  
        if (pattern.matcher(sql).find()) {  
            return SQL_TYPE_INSERT;  
        }  
        pattern = Pattern.compile(SQL_TYPE_PATTERN_UPDATE, Pattern.CASE_INSENSITIVE);  
        if (pattern.matcher(sql).find()) {  
            return SQL_TYPE_UPDATE;  
        }  
        return SQL_TYPE_OTHER;  
    }  
  
}
```

2. 然后在 *MybatisConfiguration* 配置类中，添加配置
```java
@Autowired  
private List<SqlSessionFactory> sqlSessionFactories;  
  
@PostConstruct // Spring Bean的生命周期方法，会在构造方法、自动装配之后自动执行  
public void addInterceptor() {  
    Interceptor interceptor = new InsertUpdateTimeInterceptor();  
    for (SqlSessionFactory sqlSessionFactory : sqlSessionFactories) {  
        sqlSessionFactory.getConfiguration().addInterceptor(interceptor);  
    }  
}
```