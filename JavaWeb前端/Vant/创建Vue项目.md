
参考第四阶段创建Vue项目的步骤,创建Vue项目  
  
首先确定一个要创建项目的文件夹  
  
例如在D盘下创建vue-home文件夹  
  
进入文件夹,在地址栏输入cmd 打开dos命令行界面  
```0  
D:\vue-home>vue create demo-vant  
```  
  
创建时具体选项,参照之前四阶段的笔记即可  
  
下面我们就可以用idea打开我们创建的项目了


## 安装Vant支持  
  
我们创建的Vue项目并*不会默认就支持Vant*。所以,我们需要 **安装Vant的支持** 到Vue项目中：  
1. 在打开的idea界面最下方,找到 *Terminal* 点击   
2. 在出现的命令行中输入**安装Vant的指令**，运行安装Vant的npm命令:(-S的S是大写的!!!!!!!)：
```  
npm i vant@latest-v2 -S  
```  
安装结束后可能有警告,可以忽略  
  
最终可能看到下面的结果  
```  
added 4 packages in 3s  
```

到此为止,我们就将Vant运行需要的文件安装在vue项目中了

老师git地址:  
https://gitee.com/jtzhanghl/vant2210.git


## 添加Vant引用  
  
如果想要在项目中 **使用Vant提供的组件** ，需要在Vue项目代码中*添加Vant的引用*  
  
Vue项目的 *src/main.js* 文件中,添加引用代码如下：
```js  
import Vant from 'vant'  
import 'vant/lib/index.css'  
  
Vue.use(Vant)  
```  

添加了上面的引用,当前Vue项目就可以使用Vant组件了  

为了实时运行项目,我们先启动Vue项目,测试表示它正常运行

可以在idea提供的Terminal界面中编写如下代码，如果是拉取别人的项目需要先运行npm install  
```  
npm run serve
```  
  
打开浏览器，输入localhost:8080，进入移动端页面调试模式  
  
以edge浏览器为例按F12进入调试模式后点击*移动端调试*即可  
![[移动端调试.png]]

