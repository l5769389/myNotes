#  2. 添加样式

## 2.1 选择器

1. 子选择器和同辈选择器:

* `#nav > li 子选择器`

* `h2+ p 相邻同辈选择器`

* `h2 ~ p 一般同辈选择器：选择h2后所有的p`



2. 属性选择器

* `a[href^="http:"]`:以http开头的
* `img[src$=".jpg"]`
* `a[href*="/about/"]`:选中href中包含/about/的
* `a[ref~=next]`:匹配空格分隔的属性值。
* `a[lang|=en]`开头是en或者en-的属性值。



3. 伪元素

* `::first-letter`
* `::first-line`
* `::before`
* `::after`



4. 伪类

`:link   :visited  :hover  :focus,:hover  :active`

**伪类中有一个特别有用的：`target`: **

`target能获取链接中的hash值(#id)`，在html中`<p class="comment" id="comment-3">`

`.comment:target{}`可以选中该hash值为id 的项。



5. 结构化伪类:`:nth-child`

`nth-child(-n+3)`: 选中前三个



6. 表单伪类

* `input:required{}` 有required属性的选择
* `input:optional{}` 没有required属性
* `input[type="email"]:valid` 有效email
* `input[type="email"]:invalid` 无效email



7. 权重：行内>id>class、伪类、属性>类型和伪元素









