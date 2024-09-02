
VUE是目前**最流行的前端框架**，***基于MVVM设计模式***


- VUE框架**两种用法**：
    - **多页面应用**：在 *html页面* 中 *引入 vue.js框架文件*。
    - **单页面应用**：通过 *脚手架* 的方式 *使用 VUE框架*。


- **如何引入 vue.js框架文件**
    - *两种方式*：
        1. *从* **CDN服务器** *引入框架文件*
            - 准备*框架的网址*：
                - Staticfile CDN（国内）: https://cdn.staticfile.org/vue/2.2.2/vue.min.js
                - unpkg: https://unpkg.com/vue@2.6.14/dist/vue.min.js
                - cdnjs : https://cdnjs.cloudflare.com/ajax/libs/vue/2.1.8/vue.min.js
            - *引入*：*在body中*
                `<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>`
            - ![[VUE第1种引入框架文件方式.png]]
        2. *把框架文件* **下载到本地** *后再引入*
            - *引入*：*在body中*
                `<script src="vue.min.js"></script>`
            - ![[VUE第2种引入框架文件方式.png]]
    
    - 在内存中 **怎么找到对应的元素什么的** 都是 **由框架文件来解决**。

- **创建** *Vue对象*
    - Vue对象 **相当于是** *MVVM设计模式中* 的 **VM视图模型**。
      **该对象负责** *将页面中的与元素* 和 *某个变量* 进行**绑定**，
      并且*Vue会 在内存中* **不断监听着变量值的改变**，当 *变量值发生改变时* Vue框架会*自动找到页面中的元素*，*让其跟着改变*。
      
    - **语法**
        `let/var v = new Vue({el:选择器, data:{变量:变量值}, methods:{f1..f2..})`
        - **el**：*element，元素*。**设置一个选择器**，选择元素。
        - **data**：data里面 *定义 和页面相关的 变量*，定义完后Vue会*对data里的变量*进行**监听**。
        - **methods**：*自定义的方法* 都写在methods里面。
        - 例如：
```js
<div>
    <h1>{{info}}</h1>
</div>
// 引入框架文件
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
<script>
    /*Vue对象相当于时MVVM设计模式中的VM视图模型
    * 该对象负责将页面中的与元素和某个变量进行绑定,
    * 并且Vue会在内存中不断监听着变量值的改变,
    * 当变量值发生改变时Vue框架会自动找到页面中的元素，让其跟着改变*/
    let v = new Vue({
        el:"body>div",
        data: function(){
            return{
            // 监听info
            // 当info的值发生改变，页面中h1显示的内容也发生改变
            info:"Hello Vue";
            }
        },
        methods:{
            f(){
                方法体;
            }
        },
        /*  
            此方法是Vue对象初始化过程中的方法，会由Vue对象调用，
            进入到页面 立即发请求 的代码写在此方法中
        */  
        
    });
</script>
```


- **created: function () {方法体}**：此方法是*Vue对象 初始化过程中 的方法*，会*由Vue对象调用*，当*页面加载时 执行该方法*，
    - 约等于 *刚刚创建状态*。
    - 在模板渲染成html**前**调用，即通常*初始化某些属性值*，然后再渲染成视图。
    - **进入到页面** *立即发请求* 的代码写在此方法中。
```js
methods:{
},
created: function () {
    // 发请求 通过id查询到商品的详情 并显示到页面
    // location.search：得到 地址栏中 "?"以及之后的内容  ?id=5
    // 最终请求路径是 /selectById?id=5
    axios.get("/selectById"+ location.search).then(function (response) {
        // 把服务器响应的商品对象 赋值给v.product，这样页面会自动显示处商品信息
        v.product = response.data;
    })
}
```
- **mounted(){方法体}**：与created类似，但*区别*是，该方法是：*初始化页面完成后*，在**页面加载 准备好后***再执行方法体中的代码*。
    - 约等于 *准备就绪状态*。
    - 在模板渲染成html**后**调用，通常是*初始化页面完成后*，再对html的dom节点进行一些需要的操作。
    - **更推荐这一种方法**。
    - **顺序**：*created -> 页面渲染 -> mounted*。


****


## 安装Vue插件

在设置中下载：vue.js插件


## Vue相关指令

### **文本相关指令( {{变量}} 或 v-text / v-html)**

- **{{ 变量 }}**：*插值*，让*当前位置的 文本内容* 和 *变量* 进行**绑定**。
    - *第一种*：直接插值。
        `{{info}}`
    - *第二种*：在*标签中*进行插值，区别是*受标签自带效果影响*。
        `<p>{{info}}</p>`

- **v-text = " 变量 "** ：让*元素的 文本内容* 和 *变量* 进行**绑定**，**必须写在标签里面**。
    `<p v-text = "info"></p>`

- **v-html = " 变量 "** ：让*元素的 标签内容* 和 *变量* 进行**绑定**。
    `<p v-html = "info"></p>`


### **属性绑定(v-bind  或 : 属性)**

- 语法：
    - **:属性名 = "变量"** ：让 **某个属性的值** 和 **变量** 进行*绑定*。
        - 让 *属性value的值* 和 *变量msg* 进行绑定
            - `{{msg}}`
               `<input type="text" :value="msg">`
    
    - **v-bind: 属性名 = "变量"** ：属性绑定的*复杂写法*
        - 如果*没有安装vue.js插件 会报错*，在*错误上*面 *alt+回车* 进行 *修复*。

- 如果 **属性的值** 是一个 *数值*、*布尔值*、或其他的*变量*时，需要在 *属性前面* 加上 **" : "**

- **超链接** 和 **img** 也同理
    - *超链接*：`<a :href="url">超链接</a>`
    - *img*：`<img :src="imgUrl" alt="">`


### **双向绑定(v-model)**

- *单向绑定*
    `<input type="text" :value="info">`

- **双向绑定**
    - **v-model = "变量"** ：让 **控件的值** 和 **变量** 进行*双向绑定*。
        变量值改变会影响页面，页面的改变也会影响变量
    - 例如：`<input type="text" v-model = "info">`



### **事件绑定(v-on 或 @)**

- **@事件名 = "方法"** ：让 **元素的某个事件** 和 **方法** 进行*绑定*。
    - `<input type="button" value="按钮1" @click="f()">`
    
    - *复杂写法*：
      `<input type="button" value="按钮2" v-on:click="f()">`
    
    - 该绑定后的方法与之前该绑定后的方法与之前的方法 *定义的位置不一样*，要*与 el 和 data 同级*.
        - 例：
            ```js
            let v = new Vue({  
                el: "body>div",  
                data: function(){
                    return{  
                    info: "事件绑定测试"
                    }
                },  
                /*定义绑定的方法*/  
                methods: {  
                    f(){  
                        alert("按钮点击了！")  
                    }  
                }  
            });
            ```


### **循环遍历(v-for)**

- `<标签名 v-for = "遍历时的名字 in 想要遍历的数组">{{遍历时的名字}}</标签名>;`
    - 遍历的过程中**创建当前元素**，用法*类似*Java的*新循环*。
    - 例如：`<h1 v-for = "name in arr>{{name}}</h1>`

- *遍历数组*时，显示**编号/下标**
    - `<h1 v-for = "(name,i) in arr>{{name}}</h1>`
    -  **i** 代表当前遍历数组中*对象的编号/下标*。
    - *{{ i+1 }}*：表示 *使 i 从1开始*。

- **数组添加对象**
    - `数组.push();`
        - 例如
            ```js
            //点击按钮时往数组中添加对象  
            v.arr.push({  
                name: v.persons.name,     // name：是遍历时创建的元素的属性
                salary: v.persons.salary,
                job: v.persons.job
            });
            ```

- **数组删除对象**
    - `数组.splice(删除的位置,删除的数量)`
        - 例如：
            ```js
            // 删除时，要在绑定方法的时候 传过来一个下标属性
            del(i) {  
                // 从数组中删除对象,splice(删除的位置，删除的数量)  
                v.arr.splice(i , 1)
            }
            ```

- 当数组里**装的是 对象**时如何遍历
    ```js
    <div>
    <table border="1">  
	    <caption>人物列表</caption>  
	    <tr>  
	        <th>名字</th>  
	        <th>年龄</th>  
	        <th>性别</th>  
	    </tr>  
	    <tr v-for="p in persons">  
	        <td>{{p.name}}</td>  
	        <td>{{p.age}}</td>  
	        <td>{{p.gender}}</td>  
	    </tr>
    </div>
    
    data: function{
        return{
	    persons: [{name: "刘备", age: 30, gender: "男"}, 
	    {name: "孙尚香", age: 60, gender: "女"},
	    {name: "刘禅", age: 90, gender: "男"}]
	    }
    }
    ```


### **显示隐藏指令(v-if 或 v-else 或 v-show)**

- `v-if`：让元素 **是否显示** *和 变量 进行绑定*，*true: 显示、false: 不显示* **(删除)**。
    例：`<h1 v-if="isVisible">刘德华</h1>`

- `v-else`：和 *v-if* 的 *显示状态相反*。
    例：`<h1 v-else>张学友</h1>`

- `v-show`：让元素 **是否显示** *和 变量 进行绑定*，*true: 显示、false: 不显示* **(隐藏)**。
    - 当一个元素需要 *频繁切换显示状态* 时，使用v-show*执行效率更高*。
    - 例：`<h1 v-show="isVisible">郭富城</h1>`


