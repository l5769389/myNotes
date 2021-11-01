# Vue.js 最佳实践

## 为`v-if 和 v-else 下的元素添加key`

## 路由切换组件不变

```js
const routes=[
  {
    path:'/detail/:id',
    name:detail,
    component:Detail
  }
]
```

`/detail/1` ==>`/detail/2`时，组件没有发生改变

* `vue-router 的 beforeRouteUpdate`

* ```js
  watch:{
    '$route'(){}
  }
  ```

* `<router-view></router-view> 添加Key`



## 为所有路由统一添加query

* 使用全局守卫`beforeEach`
* 使用函数劫持



## 避免同时使用`v-for  和v-if`



## 单文件组件命名：

* 组件文件名：首字母大写
* 基础组件名：Base/App/V
  * BaseButton
  * AppButton
  * VButton
* 单例组件名：该组件在每个页面中至多出现一次。
  * `TheHeading.vue`
  * `TheSidebar.vue`
* 和父组件耦合的子组件应以父组件名为前缀
  * `TodoList.vue`
  * `TodoListItem.vue`
  * `TodoListItemButton.vue`
* 组件名以一般化描述单词开头，描述性修辞结尾：
  * `SearchButtonClear.vue`
  * `SearchButtonRun.vue`
  * `SearchInputQuery.vue`
* 

