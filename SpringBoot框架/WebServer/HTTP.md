
**HTTP协议  超文本传输协议 由万维网制定(w3c)**
是浏览器与服务器通讯的*应用层协议*，
*规定了* 浏览器与服务器之间的*交互规则以及交互数据的格式信息*等。

HTTP协议对于客户端与服务端之间的**交互规则**有以下定义:
要求浏览器与服务端之间 *必须遵循一问一答的规则*，
即:浏览器与服务端建立TCP连接后需要先发送一个请求(问)然后服务端接收到请求并予以处理后再发送响应(答)。
注意，*服务端 永远不会主动 给浏览器发送信息*。

HTTP要求浏览器与服务端的传输层协议*必须是可靠的传输*，因此是*使用 TCP协议作为传输层协议*的。

HTTP协议对于浏览器与服务端之间交互的数据格式，内容也有一定的要求。
*浏览器给服务端* 发送的内容称为 *请求Request*。
*服务端给浏览器* 发送的内容称为 *响应Response*。

**请求和响应中大部分内容都是文本信息(字符串)**，
并且这些文本数据使用的**字符集**为：*ISO8859-1*。
这是一个欧洲的字符集，里面是*不支持中文*的!!!。
而实际上 **请求和响应出现的字符** 也就是 *英文，数字，符号**。


### ***客户端请求  Request***
请求是 **浏览器发送给服务端**的内容，
HTTP协议中一个请求由*三部分构成*：**请求行，消息头，消息正文**。消息正文部分可以没有。

1. **请求行**
    - 请求行是一行字符串，以*两个连续的字符*(*回车符 和 换行符*) 作为*结束这一行的标志*。（键盘上的回车也是 内置回车+换行）
    
    - *回车符：* 在ASC编码中2进制内容对应的整数是*13*.回车符通常用 *CR* 表示。
    - *换行符：* 在ASC编码中2进制内容对应的整数是*10*.换行符通常用 *LF* 表示。
    - 回车是把光标挪到最左边，换行是换到下一行。
        回车符和换行符实际上都是不可见字符。
    
    - **请求行分为三部分:**
    *请求方式(SP)抽象路径(SP)协议版本(CRLF)*    注:*SP是空格*
    例如：GET /myweb/index.html HTTP/1.1
    
    - **URL地址格式:**
    *协议://主机地址信息/抽象路径*
    例如：http://localhost:8088/index.html
    GET /index.html HTTP/1.1


2. **消息头**
    - 消息头是*浏览器给服务端 发送的一些附加信息*，
       有的用来说明浏览器自身内容，有的用来告知服务端交互细节，有的告知服务端消息正文详情等。
    
    - 消息头由 *若干行* 组成，*每行结束也是以CRLF标志*。
    
    - 每个消息头的 **格式** 为：*消息头的名字:(SP)消息的值(CRLF)*
       消息头部分*结束是以单独的(CRLF)标志*。
    - 例如:
        Host: localhost:8088(CRLF)
        Connection: keep-alive(CRLF)
        Upgrade-Insecure-Requests: 1(CRLF)
        User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36(CRLF)
        Sec-Fetch-User: ?1(CRLF)
        Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9(CRLF)
        Sec-Fetch-Site: none(CRLF)
        Sec-Fetch-Mode: navigate(CRLF)
        Accept-Encoding: gzip, deflate, br(CRLF)
        Accept-Language: zh-CN,zh;q=0.9(CRLF)(CRLF)

3. **消息正文**
    - 消息正文是*2进制数据*，通常是用户上传的信息，比如：在*页面输入的注册信息*，*上传的附件*等内容。

4. **完整的request请求大致内容：**
    GET /index.html HTTP/1.1
    Host: localhost:8088
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.130 Safari/537.36
    Sec-Fetch-User: ?1
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
    Sec-Fetch-Site: none
    Sec-Fetch-Mode: navigate
    Accept-Encoding: gzip, deflate, br
    Accept-Language: zh-CN,zh;q=0.9
    1010101101001.....
       


### ***HTTP响应  Response***
响应是**服务端发送给客户端**的内容。
一个响应包含*三部分*：**状态行，响应头，响应正文**。

1. **状态行**
    - 状态行是一行字符串(CRLF结尾)，并且状态行由*三部分组成，格式为:*
        *protocol(SP)statusCode(SP)statusReason(CRLF)*
        即：*协议版本(SP)状态代码(SP)状态描述(CRLF)*
        - 例如：HTTP/1.1 200 OK
    
    - 状态代码是一个3位数字，分为5类：
        - **1xx**：*保留*。
        - **2xx**：*成功*，表示处理成功，并*正常响应*。
        - **3xx**：*重定向*，表示处理成功，但是*需要浏览器进一步请求*。
        - **4xx**：*客户端错误*，表示*客户端请求错误*导致服务端无法处理。
        - **5xx**：*服务端错误*，表示*服务端处理请求过程出现了错误（一般为controller出错误）*。
    
    - 具体的数字在HTTP协议手册(苍老师网站)中有相关的定义，可参阅。
       状态描述手册中根据不同的状态代码有参考值，也可以自行定义。通常使用参考值即可。
    
    
2. **响应头**
    *响应头* 与 *请求中的消息头* **格式一致**，表示的是*服务端发送给客户端的附加信息*。
    
3. **响应正文**
    *2进制数据*部分，包含的 *通常是客户端实际请求的资源内容*。
    
4. **响应的大致内容**
    HTTP/1.1 404 NotFound(CRLF)
    Content-Type: text/html(CRLF)
    Content-Length: 2546(CRLF)(CRLF)
    1011101010101010101......
    
    **这里的两个响应头:**
    *Content-Type：* 是用来 告知浏览器 *响应正文中的内容* 是 *什么类型的数据*(*图片，页面等等*)
    *不同的类型对应的值是不同的*，
    比如:
    *文件类型*       *Content-Type对应的值*
    html              text/html
    css                 text/css
    js                    application/javascript
    png                image/png
    gif                  image/gif
    jpg                 image/jpeg
    
    *Content-Length：* 是用来告知浏览器*响应正文的长度*，单位是*字节*。
    
    浏览器 *接收正文前* 会 *根据上述两个响应头* 来 *得知长度和类型* 从而 *读取出来做对应的处理* 以
    显示给用户看。
