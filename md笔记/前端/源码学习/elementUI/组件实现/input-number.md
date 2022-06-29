# 数据流



![input-number](/Users/ljun/Desktop/论文资料/input-number.svg)





# 样式方面

## 几个样式选择器

1. `[class*=el-icon]` 选择了所有含有el-icon的类名。
2. `[class^=el-icon]`选择了所有以el-icon开头的类名。
3. `[class$=el-icon]`选择了所有以el-icon结尾的类名。

这样其实避免了，要为如下设置样式时遇到的问题：

```
.btn{
}
.btn{
	&.left{
	
	}
	&.right{
	
	}
}

<div class ="btn left">
<div class ="btn right">

==>

<div class ="btnleft">
<div class ="btnright">

[class*=btn]{
	//...
}
```

