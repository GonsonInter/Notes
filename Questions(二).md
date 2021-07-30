## 1. canvas元素拖拽、点击

- 第一种，利用图层给要拖动的元素的整个canvas添加拖动或点击的事件处理函数；
- 第二种，拖动时每次擦掉重新绘制，如果只需要拖动其中某一个元素则分图层。

> **如何知道鼠标在canvas元素上**
>
> - 对于规则图形可以直接使用数学方法判断
> - 对于复杂图形，可以使用ctx.getPointPath(x, y)方法
>
> ```js
> // 监听鼠标事件
> cvs.onmousemove = function (e) {
>   // 得到鼠标的坐标
>   var x = e.pageX, y =e.pageY;
>   ctx.clearRect(0,0,400,300)
>   // 遍历绘制图形
>   for(var i = fns.length; i--;) {
>     fns[i]();
>     // 每绘制一个图形就判断一次当前鼠标的坐标是否在这个图形上，然后进行自定义操作
>     if(ctx.isPointInPath(x,y)) {
>       ctx.fillStyle = "#f00"
>     } else {
>       ctx.fillStyle = "#000"
>     }
>     ctx.fill()
>   }
> }
> ```
>
> 

