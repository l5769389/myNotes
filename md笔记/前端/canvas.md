[参考博客](https://juejin.cn/post/6922402555142242311#heading-7)

# 基础canvas

```vue
        <canvas
            class="canvas"
            ref="canvas"
            @mousedown="handleMouseDown"
            @mousemove="handleMouseMove"
            @mouseup="handleMouseUp"
            @mouseleave="handleMouseLeave"
        />
```



# vue 引入canvas图片



```js
import img from '../../assets/images/icons/arrow_left_active.png'
      const imgSrc =new Image();
      imgSrc.src =img;
      imgSrc.onload=function () {
        ctx.drawImage(imgSrc,0,0,100,100)
      }
```



# canvas选中事件触发对象

## 获取操作点在canvas中的位置

```js
    getPointCanvasPosition(e){
        let {left,top} = this.$refs.canvas.getBoundingClientRect();
         return {
           x:e.clientX -left,
           y:e.clientY -top,
         }
    },
```

## 判断当前操作点是哪个顶点

```js
   // 检测是否在某个顶点内
    checkPointIndex (x, y) {
        let result = -1
        // 遍历顶点绘制圆形路径，和上面的绘制顶点圆形的区别是这里不需要实际描边和填充，只需要路径
        this.pointsArr.forEach((item, index) => {
            this.ctx.beginPath()
            this.ctx.arc(item.x, item.y, 6, 0, 2 * Math.PI)
            // 检测是否在当前路径内
            if (this.ctx.isPointInPath(x, y)) {
                result = index
            }
        })
        return result
    }
```



## 判断是否在目标区域内

**`ctx.isPointInPath(x,y)`**

```js
// 检测是否在四边形内
checkInPolygon(x,y){
  ctx.beginPath();
  this.horizonOrVerticalPointsList.forEach((item,index)=>{
      if (index ===0){
        ctx.moveTo(item.x,item.y)
      }else {
        ctx.lineTo(item.x,item.y)
      }
  })
  ctx.closePath();
  return ctx.isPointInPath(x,y)
},
```

