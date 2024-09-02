
*官方文档*：[Vant 2 - AddressList 地址列表](https://youzan.github.io/vant/v2/#/zh-CN/address-list)

```html
<template>  
  <div>  
    <van-address-list  
        v-model="chosenAddressId"    // 绑定 选择的地址的id
        :list="list"  
		
		// 超出配送范围的列表
        :disabled-list="disabledList"  
        disabled-text="以下地址超出配送范围"  
  
        default-tag-text="默认"  
        @add="onAdd"  
        @edit="onEdit"  
    />  
  </div>  
</template>  
  
<script>  

// 消息提示时用
import {Toast} from 'vant';  
  
export default {  
  data() {  
    return {  
      chosenAddressId: '1',  
      list: [  
        {  
          id: '1',  
          name: '张三',  
          tel: '13000000000',  
          address: '浙江省杭州市西湖区文三路 138 号东方通信大厦 7 楼 501 室',  
          isDefault: true,  
        },  
        {  
          id: '2',  
          name: '李四',  
          tel: '1310000000',  
          address: '浙江省杭州市拱墅区莫干山路 50 号',  
        },  
      ],  
  
    // 禁用列表
      disabledList: [  
        {  
          id: '3',  
          name: '王五',  
          tel: '1320000000',  
          address: '浙江省杭州市滨江区江南大道 15 号',  
        },  
      ]  
  
    };  
  },  
  methods: {  
    onAdd() {  
      Toast('新增地址');  
    },  
    onEdit(item, index) {  
      Toast('编辑地址:' + index);  
    },  
  },  
};  
</script>
```