
**作用：给页面添加*动态效果*。**

- **语言特点：**
    - 属于*脚本语言*，*不需要编译*，直接由*浏览器解析执行*。
    - 属于*弱类型语言*。
    - *基于面向对象*的语言。
    - *安全性强*：JS语言*只允许*访问*浏览器内部*的数据，*浏览器以外*的数据*禁止*访问。
    - *交互性强*：因为JS语言是*嵌入到HTML页面* 中*直接与用户进行交互*。

- 编程语言包括：变量，数据类型，运算符，各种语句，方法，面向对象
   JS也是这些。

## **变量**

**JS语言**属于**弱类型语言**，**声明变量**的时候**不需要指定类型**。

- 通过 **let** 或 **var** 关键字**声明变量**：
    - *let声明的变量* 作用域 *和 Java* 语言类型*一样*。
    - *var声明的变量* 作用域 *相当于* Java中的*全局变量*，可以在页面中任何地方调用。
    - 举例：
        ```java
        java:
        for(int i=0; i<5; i++){
	        int j = i + 1;
        }
        int x = i + j;    // i 和 j 都 超出了作用域，编译报错
        
        JavaScript:
        for(let i=0; i<5; i++){
	        int j = i + 1;
        }
        let x = i + j;    //不报错 但是 访问不到 i 和 j 因为超出了作用域
        
        for(var i=0; i<10; i++){  
	        var j = i + 1;
        }  
        var x = i + j;    //不报错 并且 可以访问 i 和 j 的值
        ```


## **数据类型**

JS语言中**只有 对象类型**。

- 常见的对象类型：
    - *String 字符串*：可以用 *单引号 或 双引号* 修饰。
        - `"abc" 或 'abc'`
    - *number 数值*：相当于Java中*所有数值类型的总和*。
    - *boolean 布尔值*：*true 和 false*。
    - *undefined 未定义*：当变量*只声明不赋值*的时候，变量为未定义类型。

- **获取**变量的*数据类型* 的方式：
    - `typeof 变量名;`

- **转换** *对象类型* 的 *方法*：
    - 将 *字符串* 转为 *数值*：
        - `parseFloat(字符串);`
        - 例如：
            - `s.innerText = parseFloat(i.value) + parseFloat(i.value);`
        - 或JavaScript中*字符串*进行*减 乘 除 运算*时，会*自动* 将*变量转成 数值*，如果*不是数值* 则会*转成NaN*。*加法运算* 是 *拼接*。
          如果*需要数值相加*的话，则 *转换类型* 或 *先进行 减乘除运算 再进行 加法运算*。
            例：`s.innerText = i.value * 1 + i.value * 1;`


## **运算符**

- **算数运算符：** `+ - * / %`
    - JS *除法运算* 会 *自动根据结果* 转成 *整数或小数*。
        - Java：int x = 5;  int y = 2;  int z = x/y;     z = 2;
        - JS：     let x = 5;  let y = 2;   let z = x/y;     z = 2.5;

- **关系运算符：** `> < >= <= == != == 和 ===`
    - *== ：先统一 等号两边 变量的类型* 再比较值
        - `"666" == 666;`      true
    - *=== ：先比较类型*，*类型相同* 之后 *再比较值*
        - `"666" === 666;`     false

- **逻辑运算符：** `&& || !`
    - 只支持 *短路与* 和 *短路或*。

- **赋值运算符：** `= += -= *= /= %=`

- **三元运算符：** `条件 ? 值1 : 值2;` 


## **各种语句**

- **if else**
- **for**
- **while**
- **switch  case**

**都和Java一样。**

## 如何在html页面中添加JS代码

- 和CSS一样有**三种引入方式**：
    - *内联*：在元素的 *标签的 事件 属性* 中*添加 js代码*, 当事件*触发时执行*。
        - **事件**：**系统**给**提供的一系列时间点**
            - *点击事件*：当用户 *点击某个元素时触发* 的 *时间点*。
                - 例：当点击事件触发时，*弹出提示框*。
                    - `onclick="alert('按钮点击了')"`
    
    - *内部*：在 *html页面* 的 *任意位置* **添加script标签**，在*标签体内 写js代码*。
    
    - *外部*：在*单独的 JS文件* 中写 *JS代码*，在html页面中*通过 script标签 引入并执行*。


## **方法**

Java：`public 返回值类型 方法名(参数列表){方法体}`
JS：`function 方法名 (参数列表){方法体}`

- 常见的**四种方法**：
    - 无参无返回值
    - 有参无返回值
    - 无参有返回值
    - 有参有返回值
    - 例：
        ```js
        // 无参无返回值  
        function f1() {  
            console.log("方法f1执行！");  
        }  
        // 调用方法  
        f1();  
          
        // 有参无返回值  
        function f2(name, age) {  
            console.log(name + ", " + age);  
        }  
        // 调用方法  
        f2("张三", 22)  
          
        // 无参有返回值  
        function f3() {  
            return "我是返回值";  
        }  
        let info = f3();  
        console.log(info);  
          
        // 有参有返回值  
        function f4(i, j) {  
            return i + j;  
        }  
        let sum = f4(1, 2);  
        console.log(sum);
        ```


- JS中**三种** *定义方法* 的 **方式**
    - *第一种* 定义方法的方式
        - `function 方法名(参数){方法体}`
    - *第二种* 定义方法的方式
        - `let/var 方法名 = function(参数){方法体};`
        - 例
            ```js
            // 第二种定义方法的方式  
            let f5 = function (name,age){  
                console.log("第二种: " + name + ", " + age);  
            }
            ```
    
    - *第三种* 定义方法的方式
        - `let/var 方法名 = new Function("参数1","参数2"... ,"方法体");`
        - 例：
            ```js
            // 第三种定义方法的方式
            let f6 = new Function("name", "age"  
                ,"console.log( '第三种: '+ name + ', ' + age )" );  
            f6("关羽", 70);
            ```


## **页面相关方法**

- 通过 **选择器** 查找页面中的 *元素对象*
    - `let/var 元素对象 = document.querySelector("选择器");`

- **获取 或 修改** 元素的 *文本内容*（*不赋值* 就是获取，*赋值* 就是修改）
    - *获取*元素的文本内容：
        - `元素对象.innerText;`
    - *修改*元素的文本内容：
        - `元素对象.innerText = "xxx";` 

- **获取或修改** *控件* 的 *值*，(控件：文本框，密码框，单选，多选，下拉选等)
    - *获取* 控件的值：
        - `控件对象.value;`
    - *修改* 控件的值：
        - `控件对象.value = "xxx";`


## NaN

**Not  a  Number：** *不是一个数*，*NaN* 和 *任何数值* 进行 *运算* 得到的 *结果都是NaN*。

- 例如：
    - `i.value   "5" * "5" 即 5 * 5 = 25`
    - `i.value   "a" * "5" 即 NaN * 5 = NaN`

- 判断变量是否为NaN
    - `boolean isNaN(变量);`
    - 返回值为Boolean类型，*true* 则代表 *是NaN*。

- JavaScript中*字符串*进行*减 乘 除 运算*时，会*自动* 将*变量转成 数值类型*，如果*不是数值类型*则会*转成NaN*。
    - 但字符串进行 *加法运算* 是 *拼接*。
        - 如果字符串*需要让数值进行相加*，则进行*数据类型转换*
            - `parseFloat(字符串);`    将字符串 **转成** *整数或小数*。
            - 例如：`s.innerText = parseFloat(i.value)+parseFloat(i.value);`


## JavaScript中对象分类

**内置对象**：*number，string，boolean等*。

**BOM**：Browser  Object  Model  *浏览器对象模型*，包含*和浏览器相关*的内容。

**DOM**：Document  Object  Model  *文档对象模型*，包含*和页面相关*的内容。


### **BOM浏览器对象模型**

#### window对象

**window对象**：该对象中的 *属性* 和 *方法* 称为 *全局属性* 和 *全局方法*，*访问*的时候*可以省略掉window.*。

window 对象是一个全局对象，是JS的**最顶层对象**，*其他的BOM对象*都是*window对象*的*属性*；

- window对象中的**常见方法**：
    - **isNaN(变量)**：*判断* 变量 *是否为NaN*。
    - **parseInt(字符串)**：将*字符串或小数* 转成 **整数**。
    - **parseFloat(字符串)**：将*字符串* 转成 **小数或整数**。
    
    - **alert("xxx")**：弹出*提示框*。
    - **confirm("xxx")**：弹出*确认框*。确认为true，取消为false。
    - **prompt("xxx")**：弹出*文本框*。
    
    - **setInterval(方法, 时间间隔)**：*开启* 定时器。
    - **clearInterval(定时器对象)**：*关闭* 定时器
        - 例：
            1. `let timer = setInterval(f,1000);`    开启定时器
            2. `clearIntervar(Timer);`    关闭这个定时器
    - **setTimeout(方法, 时间间隔)**：开启 *只执行一次* 的定时器。

- window对象中**常用的属性**
    - *location 位置*
        - **location对象**保存和 *浏览器请求地址（URL）* 相关的信息
        - **location.href**：*获取或修改*浏览器的*请求地址*。
            - 例如：`location.href="/index.html";`
        - **location.reload()**：浏览器*重新加载*（刷新）。
        - **location.search**：返回 *地址栏中 "?"以及之后的内容*
    - *history 历史*
        - **history.length**：得到*历史页面数量*。
        - **history.back()**：返回*上一个*页面。
        - **history.forward()**：前往*下一个*页面



### **DOM 文档对象模型**

DOM里面包含的都是**和页面相关的内容**

- **页面相关方法**
    - 通过 **选择器** 查找页面中的 *元素对象*
        `let/var 元素对象 = document.querySelector("选择器");`
    
    - **获取 或 修改** 元素的 *文本内容*（*不赋值* 就是获取，*赋值* 就是修改）
        - *获取*元素的文本内容：
            `元素对象.innerText;`
        - *修改*元素的文本内容：
            `元素对象.innerText = "xxx";` 
    
    - **获取或修改** *控件* 的 *值*，(控件：文本框，密码框，单选，多选，下拉选等)
        - *获取* 控件的值：
            `控件对象.value;`
        - *修改* 控件的值：
            `控件对象.value = "xxx`
    
    - **创建** *元素对象*
        - `let 元素对象 = document.creatElement("标签名");`
            例：创建一个图片标签：
                `let i = document.creatElement("img");`
    
    - 为 *元素对象的属性* **赋值**
        - `元素对象.属性 = xxx ;`
            例：为图片标签赋值：
                `i.src = "../11.jpg;"
    
    - 将 *元素对象* **添加** *到某个元素* 中
        - 当 *要添加到的元素* **已被** *查找到/创建的对象* 则：
            - `要添加到的元素对象.append(元素对象);`
                例：将图片标签添加到div里：
                    `let d = document.querySelector("div");`
                    `d.append(i);`
        
        - 当要 **添加** *到body* 里时：
            - `document.body.append(元素对象);`
            - *获取*页面中*body*的方式
                `document.body`
            例：将图片添加到body中
                `document.body.append(i)`
