# input

## 构成方式









## 样式方面



## 实现的注意点

### composition、 input 、 change事件

input是输入触发，change是失去焦点触发。



### 对于props都通过computed进行转换

如：

```
<el-input
    clearable>
</el-input>
```

在input的实现中：

```js
  computed:{
    showClear() {
        return this.clearable &&
          !this.inputDisabled &&
          !this.readonly &&
          this.nativeInputValue &&
          (this.focused || this.hovering);
    },
```

这样clearable的添加控制了组件内部clear图标的显示，且其其他显示要素如是否有值等均由组件内部控制。





### 属性传递

```vue
<el-input
     placeholder="请输入内容">
</el-input>
```

```js
    <input
      v-bind="$attrs"
    >
```

$attrs把<el-input>中的属性就传递了进来。





### 密码切换

type=‘text’  和type=“password”



### textarea 自动调整高度

1. textarea 有个属性 resize 可以控制其是否可以调整。

参看：`packages/input/src/calcTextareaHeight.js`文件：

* 创建一个hiddenTextarea，添加到dom最后
* 通过`calculateNodeStyling(targetElement)`来获取目标textarea的样式属性：` { contextStyle, paddingSize, borderSize, boxSizing }`。
* 复制上一步得到的style和目标的value给`hiddenTextarea`
* 通过一些padding和border计算出hiddenTextarea的高度。
* 将hiddenTextarea置空，来获取单行高度。
* 比较设置的限高和scrollHeight









# 一些值得注意的点

<img src="/Users/ljun/Library/Application Support/typora-user-images/image-20211107144506581.png" alt="image-20211107144506581" style="zoom: 25%;" />

如图所示，在输入的情况下其实setVal被触发了2次。

我觉得在源码中：handleInput 不需要再`this.$nextTick(this.setNativeInputValue);`不知道目的是什么。

