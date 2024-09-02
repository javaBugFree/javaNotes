
**同步**：指*单线程* 一次做几件事
**异步**：指*多线程* 同时做几件事


**同步请求**：指**客户端 只有一个主线程**，主线程需要负责页面的 **渲染操作** 以及 **监听操作**。
如果需要*发出请求*时，主线程会*停止渲染（清空页面）然后发出请求*，直到服务器*响应了数据之后* 才会将服务器相应的数据*再次渲染*出来。这样把原内容清空掉显示新的内容称为*页面的整体刷新*，**同步请求 只能实现 整体刷新，无法实现页面的 局部刷新**。

*form表单* 发出的就是*同步请求*。

**异步请求**：指**客户端主线程**负责*页面的渲染和监听*，如果**需要发出请求时**，*创建一个新的子线程*，由*子线程发出请求*，请求到数据后*把数据呈现在原页面的基础之上*。这种对原页面的*部分内容进行改动* 称为*页面的局部刷新*，**只有通过异步请求才能实现页面的局部刷新**。


## 客户端如何发出异步请求

之前发出请求的方式都是同步请求

- **通过Axios框架发出异步请求。**
    - **html页面中引入 Axios框架 和 Vue框架**
```html
<!--引入Axios 和 Vue框架-->
<script src="https://cdn.bootcdn.net/ajax/libs/axios/0.21.1/axios.min.js"></script>
<script src="https://cdn.staticfile.org/vue/2.6.14/vue.min.js"></script>
```

- **相关代码**
```js
f(){
    // 发出异步get请求
    axios.get("/helloAxios").then(function (response) {
        // response是响应对象，response.data得到服务器相应的数据
        alert(response.data)
        v.info = response.data;
    })
}
```

**处理请求时**，不论是同步请求还是异步请求，*处理请求方式都一样*。

**开启一个新的线程**来*发送请求*，主线程该渲染页面就渲染页面，*原页面不动*

当**服务器响应后**，开始执行 *then(function(response){})* 里面的代码

**response**代表*响应对象*，里面的 **data属性** 代表 *服务器响应的数据*。

**Controller** 处理方法 *return 了什么*，客户端**response.data** *接收到的就是什么*。


### 发出异步 get 请求

**开启一个新的线程**来*发送请求*，主线程该渲染页面就渲染页面，*原页面不动*

**get("/helloAxios")**：指定*请求的地址*

**参数** 是写在 *请求地址的后面*，*通过 "?" 连接*，多个参数 *通过 "&" 分隔*。
例如：`get("/getAxios?name=tom&age=18")`

methods：
```js
f(){
    /*
        发出异步 get 请求
        开启一个新的线程来发送请求，主线程该渲染页面就渲染页面，原页面不动
        get("/helloAxios")：指定请求的地址
        参数是写在 请求地址的后面，通过 "?" 连接，多个参数通过&分隔
    */
    axios.get("/getAxios?name=tom&age=18").then(function (response) {
        // response代表响应对象，里面的data属性代表服务器响应的数据
        // Controller处理方法 return 了什么，response.data接收到的就是什么
        alert(response.data);
        v.info = response.data;
    })
}
```

Controller：
**接收参数**直接在 *参数列表* 声明 *同名的变量* 即可
```java
@RequestMapping("/getAxios")
// 接收参数直接在参数列表声明 同名的变量 即可
public String getAxios(String name,Integer age){
    // return 了什么，客户端response.data接收到的就是什么
    return "服务器接收到了get请求！name= " + name + ", age= " + age;
}
```


### 发出异步 post 请求

参数是存放在 **请求体** 里面的，post请求时 *参数 封装到  JS对象 里面*。
例如：`post("/postAxios",data)`

methods：
```js
f2(){  
    /*
        发出异步 post 请求
        参数是存放在 请求体 里面的，post请求时 参数封装到 JS对象 里面
    */
    let data = {name: "jerry", age:18};
    axios.post("/postAxios",data).then(function (response) {
        // 把服务器相应的数据显示到页面中
        alert(response.data);
        v.info = response.data;
    })
}
```

Controller：
如果客户端发出的是 **post 请求**，并且*参数是 自定义的 JS对象*
服务器**接收参数时**需要 *同样自定义一个对象 来接收*，并且 *在参数列表* 通过 **@RequestBody注解**修饰
```java
// 如果客户端发出的是 post 请求，并且参数是 自定义的JS对象
// 服务器接收参数时需要 同样自定义一个对象 来接收，并且 在参数列表 通过@RequestBody注解修饰
@RequestMapping("/postAxios")  
public String postAxios(@RequestBody Person person){  
    return "服务器接收到了post请求！" + person.toString();
}
```