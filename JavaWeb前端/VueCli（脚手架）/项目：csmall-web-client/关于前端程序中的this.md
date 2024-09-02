
在VUE脚手架项目中，`this` 关键字指的就是Vue对象，需要通过 `this` 关键字调用的有：

- 在 `main.js` 中，通过 `Vue.prototype.xxx` 声明的属性，需要通过 `this.xxx` 进行调用。
    - 例如：项目中使用的 `this.axios`、`this.qs` 等。

- 在JavaScript程序中，通过 `export defalt {}` 中的 `data() 函数` 的 `return{}` 中声明的属性。
    - 例如：项目中使用的 `this.ruleForm` 等。

- 在JavaScript程序中，通过 `export defalt {}` 中的 `methods` 属性中声明的函数。
    - 例如：项目中使用的 `this.resetForm(formName);` 等

- 其他的固定用法，通常是由 VUE 或 某些特定框架 注册到Vue对象中的属性
    - 例如项目中使用到的 `this.$message.error(responseBody.message);`