

# 基本概念

## 容易忽视的点

### padding、margin 的百分比依据都是**父元素宽度**

```css
.block{
  margin-right:5%   // 是其父元素宽度的5%
}
```



### 块级盒子、行内盒子、行盒子和匿名盒子

块级盒子沿垂直方向堆叠

行内盒子沿文本流水平排列，会换行。**水平方向上的间距收到：padding、border、margin的影响。但是垂直方向不受这三者影响，仅受到line-height的影响。width和height也不会起作用。**



**行盒子**指的是：由一行文本形成的水平盒子。

匿名盒子是指：如下这种直接包裹的文字，具体分为：

	1. 匿名块盒子
	2. 匿名行盒子

```html
<div>
  text
</div>
```



### position的取值区别

static和relative的区别在于：

1. **取relative就能用top、left等属性进行位移了。**
2. 取relative/ absolute就能定位子孙absolute的位置





### float

通常情况下：

<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojr6gnzslj20he0jcwi8.jpg" alt="image-20210314212640760" style="zoom:33%;" />



但是一旦设置inner为float的，就会出现：



<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojr841urfj20hg0hkadr.jpg" alt="image-20210314212817581" style="zoom:33%;" />



如果要回到上面的样子。需要设置clear属性。指定盒子的哪一侧不应该紧挨着浮动盒子。

 **clear 是通过给该元素添加足够的marign-top实现的，所以在小于该值时无效。**

```css
    .container{
            width: 300px;
            height: 300px;
            outline:1px solid #61dafb;
            clear: both;

        }
        .inner{
            width: 100px;
            height: 100px;
            float: left;
            outline:1px solid red;
            clear: both;
        }
        .inner1{
            width: 50px;
            height: 50px;
            outline: 1px solid blue;
            clear: left;
            margin-top: 10px; // clear 是通过给该元素添加足够的marign-top实现的，所以在小于该值时无效。
        }
```



<img src="https://tva1.sinaimg.cn/large/e6c9d24egy1gojrjadpurj20hm0i4wep.jpg" alt="image-20210314213901046" style="zoom:33%;" />









### line-height

line-height可以为像素值、百分比或者em值。但是如果设置为百分比或者em值**子元素在继承时获得的都是计算后的像素值**，如果设置为无单位值，那么继承的也是无单位值

```css
body{
  font-size:20px;
  line-height:1.5;
}
//那么子元素的line-height都是其font-size * 1.5
```



### css设置字体大小写变换

* `text-tranform:uppercase`全大写
* `lowercase`全小写
* `capitalize`首字母大写



```
font-variant: small-caps;
```

设置首字母变大，且都是大写。



| 功能     |                |      |
| -------- | -------------- | ---- |
| 单词间距 | letter-spacing |      |
|          |                |      |
|          |                |      |



#### 毛边

![image-20210324205618756](https://tva1.sinaimg.cn/large/008eGmZEgy1govahxuvc5j316s0a4wh0.jpg)



可以通过设置：`text-align:justify`来使得两端对齐。

但是这样可能会产生：**串流**，也就是单词中间的间隙变得特别大。



#### 连字符

对于较长的单词转折，可以设置：

1. `<html lang=”en“>`
2. `p{hyphens:auto}`

来实现自动连字符。



#### 多栏文本columns

> columns =column-count +column-width

<img src="https://tva1.sinaimg.cn/large/008eGmZEgy1govaphnworj32880hmaie.jpg" alt="image-20210324210335957" style="zoom:33%;" />



```css
article{
    				margin: 0 auto;
            max-width: 70em;
            columns:20em;
            column-gap: 1.5em;
}
```



##### 跨栏

如上中如果想设置一个h1跨栏：

`h1{column-span: all}`





### @font-face

以下是**字体描述符**：用来描述在什么情况下才使用该字体。

1. font-family： 字体族的名称
2. src
3. font-weight：
4. font-style





# 常用实现

## 两行隐藏

```css
 overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  -webkit-box-orient: vertical;
```



# css 动画

## animation

* animation-name
* animation-duration：持续时间
* animation-timing-function

* animation-delay ：css动画合适开始，小于等于0立即开始。
* animation-iteration-count：动画在结束前的运行次数
* animation-direction：
  * normal:每个动画循环结束，动画重置到起点重新开始，这是默认属性。
  * alternate
  * reverse
* animation-fill-mode
* animation-play-state： 动画是否暂停或运行。恢复一个已暂停的动画，将从它开始暂停的时候，而不是从动画序列的起点开始在动画。
  * running
  * pause



# flex

## 常用属性

### `justify-content:`

*  默认是：`flex-start`
* 注意 `space-around`：多余的空间平分后放到每一项的两边，而`space-between`等分后放到项之间。



**如何出现一个靠左，剩下靠右的样子?**

![企业微信截图_e004ebe9-4f7f-4f4c-b458-b11a6016b065](/Users/ljun/Library/Containers/com.tencent.WeWorkMac/Data/Library/Application Support/WXWork/Temp/ScreenCapture/企业微信截图_e004ebe9-4f7f-4f4c-b458-b11a6016b065.png)

如果3个都设置了`margin-right:auto` 那么也就是等分。`margin-right:auto` 抵消了其他的排布效果。



**一个元素的水平垂直居中 也可以使用`margin:auto`的方式实现。**



### `align-items`

* 默认值是：stretch，子项目默认拉升填满可用空间。

* `baseline`: 子项中文本基线与容器基线对齐。



### flex

`flex` =`flex-basis`+`flex-grow`+`flex-shrink`

**flex-grow**：在项目中的flex-basis 去除之后，还剩下多少按照flex-grow的比例进行分配。

`flex-shrink`: $(单个的flex-shrink * flex-basis)/(所有子项目的flex-shrink * flex-basis) *（需要收缩的空间总量）$

**其实也就是单个项目的收缩比例和原始尺寸的加权后的比例。**







# 变换

> 元素本身还在原来的位置，但是被畸变场捕获，畸变后的影像发生了变换。所以不影响其他布局。**如果能用变换实现的那么性能一定是最佳的。**



**`transform-orgin`**

> 默认是边框盒子的中心为原点。**但是SVG 是的原点是元素的左上角。**

如：对一个div 设置`transform:rotate(45deg)`默认是绕着其中心旋转。如果改变了transform-orgin 则情况就不一样了。





**`tranlate`**

> tranlate的值可以为百分比。 是相对于**元素自身**，而不是父元素。

变换之后的坐标系也会随之发生变化。

注意：

```css
.thing{
  transform:translate(0,100px)
}
.thing:hover{
  // 会删除上面的平移效果
  transform:rotate(45deg)
}
.thing:hover{
  transform:translate(0,100px) rotate(45deg)
}


```





# matrix ()函数





# 过渡





# css计数器 

**css计数器只能和content一起使用，cotent和before、after伪元素使用。**

## counter-reset

```vue
<template>
  <div class="home" >
  </div>
</template>
<style lang="scss" scoped>
   .home{
     counter-reset: small-apple 3; //初始为3
     counter-increment: small-apple;
     &:before,&:after{
       counter-increment: small-apple; // before 先自增一次。after是自增后的了。
       content:counter(small-apple) ;
     }
   }
</style>
```





## 实现标题嵌套

![image-20210816160446714](https://i.loli.net/2021/08/16/hZEzADjXpnHTvSO.png)

```vue
<template>
  <div class="reset">
      <div class="counter">一级标题</div>
      <div class="counter">一级标题</div>
      <div class="reset">
        <div class="counter">二级标题</div>
        <div class="counter">二级标题</div>
      </div>
  </div>
</template>
<style lang="scss" scoped>
  .reset{
    counter-reset: title;
  }
  .counter:before{
    content: counters(title,'-') '. ';
    counter-increment: title;
  }
</style>
```





## 进度条

```vue
<template>
 <div class="bar" style="--percent: 60;"></div>
</template>

<style>
  .bar{
    height: 20px; width: 300px;
    background-color: #f5f5f5;
  }
  .bar:before{
    display: block;
    counter-reset:progress var(--percent);
    content:counter(progress)'%' ; //显示的百分比如何由传入的var获取？
    width: calc(300px * var(--percent) *0.01); //进度条宽度如何设置？
    background: red;
    text-align: right;
    color: whitesmoke;
  }
</style>
```





# css变量

## css中设置变量

```css
 --color:red;
```



## html中设置css变量

即上面进度条例子中：

1. ```html
   <div class="bar" style="--percent: 60;"></div>
   ```



## Js中设置CSS变量

```html
   <div>
      <div  id="box" ref="box" style="border: 10px solid var(--color)"></div>
    </div>
```

```js
 this.$refs.box.style.setProperty('--color','red') //设置--color为红色
```



## JS中获取样式

```js
getComputedStyle(this.$refs.box).getPropertyValue('--color')
```





## vue中父子组件使用var

```vue
// 父组件
    <my-button
            :style="{
             '--bgcolor':'#8BAFFF'
            }"
          />

// 子组件
 background-color:var(--bgcolor);
```







# scss

* scss =sassy（时髦的） css
* sass=indented Sass 是一种早期的简化格式



## css扩展

1. 嵌套（Nested Rules）

2. 父选择器

3. 属性嵌套: 就是将属性前缀合并了。

   ```css
   .funky {
     font: {
       family: fantasy;
       size: 30em;
       weight: bold;
     }
   }
   ```

4. 占位符选择器：`%f00` 只能被`@extend`调用。



## SassScript

### 命令行交互

`sass -i 后`



### 变量$

```scss
$width: 5em;
#main{
    width:$width;
}
```



1. 可以将`$width:5em !global;`将其提升为全局变量

2. !default 类似于函数参数默认值，如果之前设置了就用之前的。

   ```scss
   $content: "First content";
   $content: "Second content?" !default;
   ```

   





### 数据类型

1. 数字: 1 10px
2. 字符串
   1. 有引号字符串: 'foo'
   2. 无引号字符串: foo
3. 布尔值
4. 空值：null
5. 数组list：1.5em 1em 0 2em
6. maps : (k:v,k1:v1)



#### 字符串

左连接和右连接有区别：

```scss
p:before {
  content: "Foo " + Bar;
  font-family: sans- + "serif";
}
```

==>

```scss
p:before {
  content: "Foo Bar";
  font-family: sans-serif; 
}
```



## 插值语句

1. 引号中使用**插值语句**#{} 起到js中${}的作用：

```scss
p:before {
  content: "I ate #{5 + 10} pies!";
}
```



```scss
p:before {
  content: "I ate #{5 + 10} pies!";
}
```



```scss
p:before {
  content: "I ate #{5 + 10} pies!";
}
```



2. **在选择器或属性名中使用变量**

```scss
$name: foo;
$attr: border;
p.#{$name} {  
  #{$attr}-color: blue;
}
```





## @extend

`@extend` 的作用是将重复使用的样式 (`.error`) 延伸 (extend) 给需要包含这个样式的特殊样式（`.seriousError`）

```scss
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```







# SVG

## 属性

### stroke-dasharray和stroke-offset

> 对于circle stroke-offset的起始位置是右侧x轴上点。

`stroke-dasharray `属性列举的是将stroke切割为虚线时，实线部分和间隔的长度：

1. `10 `: 实线和间隔都是10
2. `10 20 `实线10间隔20
3. `20 10 5`:其实是省略了循环：`20 10 5 20 10 5...`



![image-20210830093126124](/Users/ljun/Documents/myNotes/前端/C:\Users\msze0088\AppData\Roaming\Typora\typora-user-images\image-20210830093126124.png)

dashoffset这种移动其实就是函数`f(x+30)`的移动 加左减右。



#### hover及移开时进度条加载

btn hover的时候边框进度条加载也是同理。

```html
<svg class="svg">
    <line
            class="line1"
            x1="30"
           y1="30"
           x2="300"
           y2="30"/>
</svg>

<style>
     svg{
            stroke-width: 5;
            stroke: red;
            stroke-dasharray: 300 300;
            display: block;

        }
        .line1{
            stroke-dashoffset: 300;
            transition: all 2s;
        }
        .svg:hover .line1{
            stroke-dashoffset: 0;
        }
</style>
```



### loading动画

主要就是给stroke-dasharray设置一个较长的间隙。不断的移入移出间隙。

```vue
<style>
    .path{
      stroke: #409eff;
      stroke-linecap: round;
      stroke-width: 2;
      stroke-dasharray: 90,150;
      animation: my-loading-dash 2s ease-in-out infinite;

    }
  @keyframes my-loading-dash {
      0%{
        stroke-dasharray: 1,240;  //其实等价于：
        stroke-dashoffset:0;
      }
      //    0%{
      //  stroke-dasharray: 90,150;
      //  stroke-dashoffset:120;
      // }
    50%{
      stroke-dasharray: 90,150;
      stroke-dashoffset: -50;
    }
     100%{
       stroke-dasharray: 90,150;
       stroke-dashoffset: -120;
     }
  }
</style>  
//对于外层svg，也可以添加一个旋转。
//animation: loading-rotate 2s linear infinite;
<svg viewBox="25 25 50 50" class="circular">
      <circle cx="50" cy="50" r="20" fill="none" class="path"></circle>
    </svg>
```









# css动画

