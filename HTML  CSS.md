# HTML

## 1.  如何理解HTML语义化

**什么是语义化** 

​		用正确的html标签来展示相应的元素。如段落用<p>,标题用<head>等。

**为什么要关注语义化**

​		对人来说，便于团队开发和维护，代码可读性高，在没有加载CSS的时候也能呈现较好的内容结构与代码结构，易于阅读。

​		对机器来说，有利于SEO，搜索引擎的爬虫是根据标签来确定上下文权重的；其次，利于其他设备的解析，有利于无障碍阅读，提高可访问性。



## 2.  块状元素和内联元素

**display: block / table**   有div h1~h6  table  ul  ol  li  p 等，独占一行。

**display: inline / inline-block**   有 span  img  input  button 等，不独占一行。

 



# CSS

## 1. BFC的理解与应用

​	**BFC**（Block Format Context），块级格式化上下文。它是一块独立渲染的区域，其内部元素的渲染不会影响到边界以外的元素。

**形成BFC的常见条件**

1. float不是none
2. position是absolute或者fixed
3. overflow不是visible
4. display是flex   inline-block等

**BFC的常见应用**

1. 解决块级元素垂直方向的margin重叠问题

2. 清除浮动

3. 消除元素浮动后产生的重叠问题，因为浮动元素不在正常的文档流中。

   

## 2. float布局（圣杯布局、双飞翼布局）

### （1）圣杯布局和双飞翼布局

**特点**

- 三栏布局，中间一栏最先加载和渲染，内容最重要；
- 两侧内容固定，中间内容随着宽度自适应；
- 一般用于PC网页。

**技术总结**

- 使用float布局；

- 两侧使用margin负值，以便和中间内容横向重叠；

- 防止中间内容被两侧覆盖，圣杯布局是用padding，双飞翼布局是用margin。

  **圣杯布局**

```html
<style>
	  #container {
          background-color: #acafac;
          padding-left: 200px;
          padding-right: 150px;
      }
      #container .column {
          float: left;
          height: 100px;
      }
      #center {
          width: 100%;
          background-color: #77d941;
      }
      #left {
          position: relative;
          width: 200px;          /* LC width */
          background-color: #d8dc4a;
          margin-left: -100%;
          left: -200px;
      }
      #right {
          width: 150px;          /* RC width */
          background-color: #37bfab;
          margin-right: -150px;
      }
      #footer {
          clear: both;
      }
</style>
<body>
    <div id="header">header</div>
	<div id="container">
  		<div id="center" class="column">center</div>
  		<div id="left" class="column">left</div>
  		<div id="right" class="column">right</div>
	</div>
	<div id="footer">footer</div>
</body>
```

​	   **双飞翼布局**

```html
<style>
	  #container {
          background-color: #1f211f;
          width: 100%;
          height: 100px;
      }
      #container .column {
          float: left;
          height: 100px;
      }
      #main {
          width: 100%;
          float: left;
      }
      #center {
          background-color: #77d941;
          height: 100%;
          margin-left: 200px;
          margin-right: 150px;
      }
      #left {
          width: 200px;          /* LC width */
          background-color: #d8dc4a;
          margin-left: -100%;
      }
      #right {
          width: 150px;          /* RC width */
          background-color: #37bfab;
          margin-left: -150px;
      }
</style>
<body>
    <div id="container">
  		<div id="main" class="column">
    		<div id="center">center</div>
  		</div>
 		<div id="left" class="column">left</div>
  		<div id="right" class="column">right</div>
	</div>
</body>
```

### （2）清除浮动

​		某父级元素，其子元素浮动，因此没有被撑开，需要清除浮动。**清除浮动主要是为了解决，父元素因为子级元素浮动引起的内部高度为0的问题**

```html
1. 额外标签法：额外在最后一个浮动标签样式中添加一个clear:both (不推荐)，缺点是添加了无用标签，语义化差。
2. 父元素加overflow: hidden：变成了BFC，实现清除浮动(不推荐)。缺点是内容增多的时候不会自动换行，内容会被隐藏。
3. 伪元素after实现清除浮动(推荐)。
	.clearfix:after{  /*伪元素是行内元素 正常浏览器清除浮动方法*/
        content: "";
        display: block/table;
        height: 0;
        clear:both;
        visibility: hidden;
    }
    .clearfix{
        *zoom: 1;  /*ie6清除浮动的方式 *号只有IE6-IE7执行，其他浏览器不执行*/
    }

```

## 3. flex布局

### 使用flex布局画骰子

使用flex布局画一个点数为3的骰子

```html
<style>	  
	  #container {
          margin: 30px 0 0 30px;
          height: 200px;
          width: 200px;
          padding: 25px;
          background: linear-gradient(#D3DCE6, #d8dbdb);
          box-shadow: 0 0 5px #979494;
          border-radius: 10px;
          display: flex;
          justify-content: space-between;

      }

      .point {
          height: 25px;
          width: 25px;
          background-color: #333333;
          border-radius: 50%;
          box-shadow: 0 0 2px #343739;
      }

      #container>div:nth-child(1) {
          align-self: flex-start;
      }
      #container>div:nth-child(2) {
          align-self: center;
      }
      #container>div:nth-child(3) {
          align-self: flex-end;
      }
</style>
<body>
    <div id="container">
  	<div class="point"></div>
  	<div class="point"></div>
  	<div class="point"></div>
	</div>
</body>
```

效果图：<img src=".\imgs\image-20210325160741466.png" alt="image-20210325160741466" style="zoom:50%;" />



## 4. absolute和relative定位

- ralative依据自身定位，对外界的元素不会有影响；

- absolute依据最近一层的已定位元素进行定位。

  ​	**定位元素**：具有absolute、relative、fixed定位的元素，以及body



## 5. 居中问题

### （1）水平居中

- **inline元素**：父元素 text-align: center
- **block元素**：子元素 margin: auto
- **absolute定位元素**：子元素 left: 50%;  margin-left: 负宽度，（必须知道子元素的宽度）

### （2）垂直居中

- **inline元素**：父元素设置line-height的值等于父元素自己的height值
- **absolute定位元素**：top: 50%;  margin-top: 负高度，（必须知道子元素的高度）
- **absolute定位元素**：transform: translate(-50%, -50%)（不需要知道子元素的高度）(CSS3的属性)
- **absolute定位元素**：top, left, right, bottom都设为零;  margin: auto



## 6. 图文样式

### line-height如何继承

- 写具体数值，直接继承值（比较好理解）；
- 写比例，直接继承比例（比较好理解）；
- 写百分比，继承百分比计算出来的值（考点）



## 7. CSS响应式

### （1）rem是什么

- **px**：绝对长度单位，最常用；

- **em**： 相对长度单位，相对于父元素，不常用；

- **rem（font size of the root element）**： 相对长度单位，相对于根元素，常用于响应式布局。先控制好根元素的长度，然后其他的元素的长度都相对于根元素来控制，是可以做响应式的。其中，根元素是**html**元素。

  

```css
<style>
    html {
        font-size: 100px;
    }
    
    div {
        height: 0.3rem;    /*宽度为30px*/
    }
</style>
```

### （2） 响应式布局的常用方案

- **media-query + rem**： 根据不同的屏幕宽度设置根元素的 **font-size**; (media-query是CSS3的新属性)，直接全局定义。

  ```html
  <!--首先必须要声明viewport标签-->
  <meta name="viewport" content="width=device-width,initial=1.0,user-scalable=no">
  
  <!--这是一种-->
  <style>
  	@media screen and (min-width:768px) and (max-width:991px){
          html {
              font-size: 120px;
          }
  	}
      @media screen and (min-width:375) and (max-width:413){
          html {
              font-size: 110px;
          }
  	}
  </style>
  
  <!--这是另一种-->
  <link media="screen and (min-width:768px) and (max-width:991px)" rel="stylesheet" href="css/pad.css">
  
  ```

- **基于视口单位vh/vw**：

  + rem的弊端：“阶梯”性。根元素的font-size是阶梯性变化的，比如屏幕宽度375-413px之间的屏幕中根元素的font-size都是16px。

  + 网页视口尺寸

    window.screen.height——屏幕高度

    window.innerHeight——网页视口高度

    document.body.clientHeight——body高度

    vh——视口高度的1/100

    vw——视口宽度的1/100

    vmax，vmin——vh，vw的最大值和最小值

- **百分比布局（又叫流式布局）**：通过百分比单位可以使得浏览器中的组件的宽和高随着浏览器的变化而变化，从而实现响应式的效果。计算比较复杂。
- **flex布局**：兼容性较差
- **Grid布局**：兼容性较差
- **Columns栅格系统**：要依赖一些UI库



## 8. CSS3动画

​	（并不是重点，除非专门做动画）



## 9. Grid布局

​		采用网格布局的区域，称为"容器"（container）。容器内部采用网格定位的子元素，称为"项目"（item）。

```css
<div>
  <div><p>1</p></div>
  <div><p>2</p></div>
  <div><p>3</p></div>
</div>
```

​		上面代码中，最外层的`<div>`元素就是容器，内层的三个`<div>`元素就是项目。

注意：项目只能是容器的顶层子元素，**不包含项目的子元素**，比如上面代码的`<p>`元素就不是项目。Grid 布局只对项目生效。

### （1） 容器属性

- display: grid，开启网格布局，在默认情况下，容器元素都是块级元素，即独占一行。

  也可以设置为行内元素，那么 display: inline-grid

- 行宽、列宽、行高、列高

  ```css
  .container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 100px 100px 100px;
  }
  .container {
    display: grid;
    grid-template-columns: 33.33% 33.33% 33.33%;
    grid-template-rows: 33.33% 33.33% 33.33%;
  }
  ```

  