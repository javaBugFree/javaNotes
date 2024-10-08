
- **Get**：
    - *请求参数*在**请求地址**的后面，由于*参数在请求地址当中是可见*的，所以*不能传递带有敏感信息的参数*。
    - 如果**只传递一个参数**并且**非敏感信息**时，一般使用get请求，因为*方便*。
        - 参数直接写在请求地址的后面*通过 "?" 连接*，多个参数*通过 "&" 分隔* 即可。
    - Get请求参数**只能传递 几K 的数据**。（约定为最多1Kb）
    - Get响应*较快*一点。
    - **能用Get用Get，用不了时再考虑Post**。
    - *应用场景*：get是获取的意思，如果这个请求是*查询请求*，一般使用get
    - 可以用来做 **分享**、**收藏** 等 *需要URL* 的功能。

- **Post**：
    - 请求参数在*请求体里面*
    - 参数 **大小 没有限制**
    - 应用场景：**参数中有敏感信息时、上传文件时**，必须使用Post。当传递的参数为*多个参数*，将多个参数*封装到自定义的JS对象里面*进行传递时，使用Post会更方便。

- 应用场景总结：
    - 除了必须使用Post的两种情况（**1.敏感信息 2.文件上传**）。
    - 如果传递的参数是**两个参数以内**，则使用*Get请求*，如果**大于两个参数**一般使用*Post请求*。**2个参数**时使用*两者都可以*。

- `GER` / `POST` ：
    - 发出POST请求的方式：
        1. `<form method = "post">`
        2. `axios.post()`
        3. `js --> post`
    - 其他的全是Get请求。