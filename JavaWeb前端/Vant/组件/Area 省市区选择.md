
*官方文档*：[Vant 2 - Area 省市区选择](https://youzan.github.io/vant/v2/#/zh-CN/area)

1. 先在 views文件夹 下创建 *AreaView.vue* 视图文件，并编写代码：
```html
<template>
  <div>
    <van-area title="标题" :area-list="areaList" @confirm="showArea"/>
  </div>
</template>

<script>

// 从下载好的全国省市区数据包中提取数据，命名为areaList
import {areaList} from '@vant/area-data'

export default {
  data() {
    return {
      areaList,  // 在Vue中绑定areaList
    }
  },
  methods: {
    showArea(area) {
      console.log('选中的地址信息为：', area);
    }
  }
}
</script>
```

2. 在 *router / index.js* 中 **配置路由**
```js
{  
    path: '/area',  
    name: 'area',  
    component: () => import('../views/AreaView.vue')  
}
```

通过输入路径http://localhost:8080/area，访问省市区选择页面。但是只有少量数据

如果想要真实的数据,那么就需要在上面 *areaList数据* 中填入大量数据，
但是这个工作量很大,个人实现非常困难,所以可以使用 *业界通用* 的一个 *省市区信息对象*。

3. *安装* **全国省市区数据包**
    1. idea的Terminal界面输入如下命令，来 *安装* 全国省市区数据包
        ```
        npm i @vant/area-data
        ```
        如果有警告,直接无视。
    2. 如果该安装成功，就可以*添加*全国省市区数据到项目中了。*AreaView.vue* 代码中添加如下：
        ```js
        <script>
        // 从下载好的全国省市区数据包中提取数据,命名为areaList  
		import {areaList} from '@vant/area-data'
		
		export default {  
		  data(){    
			  return {areaList}  
		  },
		</script>
		```

4. *绑定 事件*

绑定事件方法：
|   事件    |        说明        |                  回调参数                   |
|:---------:|:------------------:|:-------------------------------------------:|
| `confirm` | 点击右上方完成按钮 |  一个数组参数，具体格式看下方数据格式章节   |
| `cancle`  |   点击取消按钮时   |                      -                      |
| `change`  |   选项改变时触发   | Picker 实例，所有列选中值，当前列对应的索引 |

用户在*点击确认*时，输出地址信息。
```html
<template>  
  <div>  
    <van-area title="标题" :area-list="areaList" @confirm="showArea"/>  
  </div>  
</template>

<script>
export default {   
  methods: {  
    showArea(area) {  
      console.log('选中的地址信息为：', area);  
    }  
  }
}
</script>
```
以上，就可以实现点击确认时，输出地址信息。
