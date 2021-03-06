# 面试题

## 1. Object.prototype.toString.call的原理

​		使用Object的原型上的toString方法，这个方法拼接三个字符串 ： ‘[object’ + [[class]] + ']'。其中 [[class]] 是对象的内部属性，一般无法访问。它是一个字符串值,表明了该对象的类型。

## 2. Bigint

​		在没有Bigint之前，Number类型都是64位，能表示（-2 ^ 53 - 1）到（2 ^ 53 - 1）之间的数。超出后用Bigint表示。可以用两种方法得到，一种是在数字后面加n，另一种是构造函数。

```js
let s = 123415612311n;
let q = BigInt(13212);
```

​		需要注意的是，和Number类型相运算的时候，必须先强制类型转换。而且Bigint不能使用一元+运算符。其他用法和Number差不多。

```js
+10n;         // → TypeError: Cannot convert a BigInt value to a number
```

## 3. Map和Set

- Map是一个key: value的数据结构。还可以这样构造Map （key一定是字符串）：

  ```js
  let m = new Map([ ['1', 'value1'], ['abc', 3] ]);
  ```

  - 对象方法：set(key, val) 	get(key)	has(key)	delete(key)	clear()

  - 遍历方法：keys()——返回key迭代器；  values()——返回value迭代器

    ​					entries()——返回实体迭代器；	forEach()——使用回调函数遍历value

- Set是一个集合，存储的对象必须是唯一的。注意，它认为NaN等于NaN。

  ```js
  const mySet = new Set(['a', 'a', 'b', 1, 2, 1]);
  ```

  - 对象方法：add(value)	delete(value)	has(value)	clear()
  - 遍历方法：keys();      values();      entries();      forEach();

  注意，Set也有key和value，只不过他们相等。

## 4. 闭包的例子

- 循环输出 i

  ```js
  for (var i = 0; i < 5; ++i) {
    (function(i) {
      setTimeout(function() {
        console.log(i + " ");
      }, 1000 * i);
    })(i);
  }
  ```

- 迭代器的实现

  ```js
  //迭代器就是一个函数，也叫迭代器生成函数
  function Iterator(o){
  	let curIndex = 0;
  	let next = () => {
  		return {
  			value: o[curIndex],
  			done: o.length === ++curIndex
          }
  	}
  }
  ```

## 5. 介绍一下Vue

- 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。
- Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://cn.vuejs.org/v2/guide/single-file-components.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#libraries--plugins)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。
- 提高开发效率，帮助减少不必要的dom操作；双向数据绑定，通过框架提供的指令，前端只需要关注业务逻辑，不再关心dom如何渲染。
- 采用了MVVM的架构，让开发更加方便。

## 6. position的常用属性

- static：无特殊定位，对象遵循正常的文档流。top、right等几个属性没有作用；
- relative：对象遵循正常的文档流，top、right等几个属性是相对与自己在文档流中的位置作用；
- absolute：对象脱离了文档流，top、right等属性相对于最近的已定位元素；
- fixed：对象脱离文档流，top等属性相对于视窗位置；
- sticky：粘性定位。

## 7. string api

lastIndexOf(searchString, position)		charAt()		charCodeAt()		slice(start, end)

split(seperator, linmit)   // linmit表示分割后数组的最大长度		substr(start, length)

substring(start, end)		replace(search, replace)		match(regExp)		search(regExp)

toLowerCase()		toUpperCase()		concat(str)

## 8. Array api

push  pop  shift  unshift  	slice(start, end)		concat([])		reverse()		sort()		join()

toLocaleString/toString/valueOf  可看作是join的特殊用法，不常用		indexOf()		lastIndexOf()		forEach()		map()		filter()		every() 		some()		reduce()		reduceRight()

splice(index, howmany ,item1,....., itemX)

## 9. 内存泄漏问题

### （1） 什么是内存泄漏

​		程序运行在内存中，只要程序提出需求，操作系统或运行时（runtime）就尽必须提供给它内存。而使用完毕后没有释放，则造成内存泄漏。内存占用越来越高，最终会导致性能变差，延迟变大，严重还会导致系统崩溃等。

### （2） 内存泄漏识别方法

​		F12 ——> memory

### （3） 造成原因

- 意外的全局变量
- dom在清空时，还保存着它的引用
- 定时器setInterval没有被清除，其中的变量不会被释放
- 循环引用

### （4） 解决办法

- 尽量减少全局变量的使用，及时对无用的数据进行垃圾回收，即设置为null
- 注意程序逻辑，避免死循环
- 避免创建过多的对象
- 减少层级过多的引用

## 10. 实现一个红绿灯

### （1） 纯CSS实现

​		对三个div分别设置动画，让它们时间相同，分别错开设置背景颜色的变化。

### （2） 封装promise

```html
<div id="lamp">
  <div></div>
  <div></div>
  <div></div>
</div>
```

```js
function changeColor(num) {
  let lights = document.querySelectorAll('#lamp>div');
  let colors = ['red', 'yellow', 'green'];
  lights.forEach(item => item.style.backgroundColor = '#5d6378');
  lights[num].style.backgroundColor = colors[num];
}

function light(num, interval) {
  return new Promise(resolve => {
    changeColor(num);
    setTimeout(resolve, interval)
  })
}

async function start(interval) {
  let i = 0;
  while (true) {
    await light(i, interval);
    if (++i === 3)  i = 0;
  }
}

start(1000);
```

## 11.  关于arguments

​		函数在调用时，浏览器会自动给函数传入两个参数，一个是this，另一个是arguments。arguments保存着函数传入的实参列表，是一个类数组对象。

​		argument有一个属性叫callee，指向函数本身。有个属性length表示列表长度。还有个属性是Symbol类型的键，该键指向的值是一个values函数，调用这个函数可以获得arguments迭代对象。

```js
function f() {
    console.log(arguments.callee === f)		// true
    let iterator = arguments[Symbol.iterator]();
}
```

## 12. 垃圾回收机制

​		JS中的内存管理是自动执行的，而且是不可见的。我们创建基本类型、对象、函数等都需要内存。

- 什么是垃圾：在JS中，没有被引用的对象就是垃圾，需要被清除。另外，如果几个对象引用形成一个环，相互引用，但是根本访问不到它们，则这些对象也是垃圾，需要被清除。
- 如何识别和回收垃圾：
  - 标记-清除算法：从根对象开始，标记每一个被它引用的对象，并记住他们，避免被重复标记。然后递归标记这些被引用的对象，直到完成所有的标记。那些没有标记的对象就是垃圾。优点是能回收那些循环引用的对象内存，缺点是每次回收都需要递归遍历，较为耗时间；
  - 引用计数法：记录每个对象被引用的次数。每次新建对象、赋值引用和删除引用的时候，更新它们的计数器，如果计数器为0的是时候，立即回收内存。因此引用计数法回收内存的速度很快，基本上是即时回收的。缺点就是计数器要占空间，循环引用无法回收。

## 13. MVC和MVVM

​		MVC是后端的分层开发概念，MVVM是前端的视图层的概念，主要关注于视图层的分离，也就是说MVVM把前端的视图层分成了三个部分，Model、View和ViewModel。

- MVC是考虑到整个项目的架构，View层调用后端的接口，接口负责处理请求，职能单一，需要处理业务的时候调用Controller层。Controller层也是职能单一的，只处理业务，不操作数据库，因此操作数据库的时候调用Model层。

- MVVM是前端的开发分层思想，Model表示状态和数据，View是最终的渲染DOM，中间层ViewModel负责监听View层的操作，并且绑定Model数据到View中去，它是一个调度者，提供了数据的双向绑定。

## 14. nginx

### （1） 正向代理和反向代理

​		正向代理是代理的客户端，隐藏真实的客户端。反向代理代理的是服务器，隐藏真实的服务器。

​		nginx反向代理的主要作用是隐藏真实的服务器、负载均衡、实现缓存等。

### （2） 工作原理

​		nginx的本质上是一个http-server，但是它仅仅是一个请求转发器。接受客户端的请求，根据客户端的请求和配置文件的配置规则，转发到相应的服务器，并且将请求的响应结果转发给客户端。

### （3） 反向代理配置

​		当Nginx安装完毕后，会有相应的安装目录，安装目录里的nginx.config为nginx的主配置文件，nginx主配置文件分为4部分，main(全局配置)、server(主机配置)、upstream(负载均衡服务器设置)以及location(URL匹配特定位置的设置),这四者的关系是：server继承main,location继承server,upstream既不会继承其它设置也不会被继承。

```js
server {
	    listen       80;
	    server_name  www.sina.com;
	    location / {
            add_header 'Access-Control-Allow-Origin' '*'
	        proxy_pass   http://tomcat1;
	        #配置默认访问页，这里就会访问到tomcat1里面的那个index.jsp文件里面
	        index  index.jsp;
	    }
	}
```

## 15. Vue 3和Vue 2的区别

- 双向绑定数据的原理发生了改变。Vue 2是使用的ES5中的Object.defineProperty()对数据进行劫持，结合发布订阅模式的方式来实现的。Vue 3使用了ES6中的Proxy API来实现对数据进行代理。

  > Proxy的优势如下：
  >
  > - defineProperty只能监听某个属性，不能对全对象进行监听；
  > - 可以省去for…in、闭包等内容来提升效率，直接绑定整个对象即可；
  > - 可以监听到数组、不需要再对数组进行特异性操作

- Vue 3 默认进行懒观察，它只对被用于渲染初始可见的部分数据创建观察者；
- Vue 3支持Typescipt
- Vue 3 的template支持多个根标签。

## 16. 为什么TCP是可靠的

- 确认重传机制：接收方收到报文之后就会发送确认包，发送方发送一段时间后没有收到确认包就会重传；
- 数据校检：发送端计算后发现TCP报文发生了变动会将其直接丢弃；
- 合理分片：TCP会将数据报合理分片并编号发出，接受方会缓存乱序到达的分片并且排序，再交给应用层；
- 流量控制：当接收方来不及处理发送方的数据时，可以提示发送方降低发送速度；
- 拥塞控制：当网络拥塞时，发送方能减少数据的发送。

## 17. 两种路由模式和原理

- Hash模式：工作原理时监听了hashchange事件；
- History模式：监听了popstate事件

## 18. clientHeight、offsetHeight、scrollHeight、offsetTop、scrollTop

- 网页可见区域高，不包括border和margin——document.body.clientHeight
- 网页可见区域高，包括border不包括margin——document.body.offsetHeight
- 网页正文全文高，不包括border和margin——document.body.scrollHeight
- 在有滚动条的时候网页被卷进去的高——document.body.scrollTop / document.documentElement.scrollTops
- offsetTop——表示自己与当前最近的已经定位的父元素的顶部之间的距离，都没有定位就是body

## 19. 数组拍平

- 递归使用 Array.prototype.concat.apply([], arr)

  ```js
  function flat(arr) {
      const isDeep = arr.some(item => item instanceof Array);
      if (!isDeep)	return arr;
      const res = Array.prototype.concat.apply([], arr);
      return flat(res);		// 递归
  }
  ```

- toString

  ```js
  let arr1 = arr.toString().split(',').map(val => parseInt(val));
  ```

- reduce实现

  ```js
  function fn(arr){
      return arr.reduce((prev,cur)=>{
          return prev.concat(Array.isArray(cur) ? fn(cur) : cur)
      },[])
  }
  ```

## 20. requestAnimationFrame

> - 要想动画看起来流畅，更新频率需要60帧，即16.67ms更新一次视图；
> - setTimeout需要手动控制频率，而requestAnimationFrame浏览器会自动控制刷新频率；
> - 后台标签或者在隐藏iframe中，requestAnimationFrame会暂停，而setTimeout则依然会执行。

```html
<div id="bar"></div>
<script>
	const $div1 = $('#div1');
    let curWidth = 100;
    const maxWidth = 640;
    
    function animate() {
        curWidth = curWidth + 1;
        $div1.css('width', curWidth);
        if (curWidth < maxWidth)
            requestAnimationFrame(animate);
    }
</script>
```

- requestAnimationFrame会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率;
- 在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的CPU、GPU和内存使用量;
- requestAnimationFrame是由浏览器专门为动画提供的API，在运行时浏览器会自动优化方法的调用，并且如果页面不是激活状态下的话，动画会自动暂停，有效节省了CPU开销。

## 21. Vue是如何实现响应式的

### （1） Object.defineProperty

```js
Object.defineProperty(obj, key, {
    set(newVal) {
        if (value !== newVal) {
            value = newVal;
        	// dep.notify();
        }
    },
    get() {
        return value;
    }
})
```

### （2） 发布订阅模式

​		当 key 对应的数据被修改时，Vue要知道该去哪里通知哪些数据也要发生改变，所以要把当前页面所有用到该对象同一key的对象加入到它通知的名单里，数据变化发布，所有订阅者将会同时刷新。

- 订阅者 Watcher

  传递一个默认值，用来辨别和其他对象的name值不一样 这里的key对应的就是name
  在自身上定义一个更新方法 传入一个字符串赋值给对应的key----name

  ```js
  class Watcher {
      // 传递后修改的值
      constructor(name) {
          this.name = name;
      }
      // 值变化时调用的方法
      update(str) {
          this.name = str;
      }
  }
  ```

- 发布者 Dep

  ```js
  class Dep {
      constructor() {
          // 创建一个数组把用到的对象的同一个key保存起来
          this.subs = [];
      }
      // 添加订阅者
      addSub(watcher) {
          this.subs.push(watcher);
      }
      // 遍历数组，并启用他们自身的update方法
      notify(str) {
          this.subs.forEach(item => {
              item.update(str);
          })
      }
  }
  ```

- 使用

  ```js
  const dep = newDep();
  const w1 = new Watcher('张三');
  dep.addSub(w1);
  dep.notify('李四');	// 通知订阅者修改
  ```

### （3） 结合

​		将data对象的值通过Object.defineProperty 中的set方法监听修改读取数据，在执行set时，既现在值发生改变，执行发布者类中的notify 将订阅者名单里的对象进行遍历调用其对象自身的update更新方法，这样修改其中一个该key对应的value，其在此页面上所有的该key对象都将执行update进行更新最新值来达到数组的响应式和一致性。

## 22. 为什么Vue组件的data是一个函数

​		.vue 文件在编译后，实际上生成一个类，每次使用这个组件的时候，实际上都会实例化一个该类的对象。如果不使用闭包，则在一处修改一个对象的值，其他的对象都会更改。因为所有的实例返回的对象指向的都是同一个。使用闭包后，每个对象中的作用域都是自己的。

## 23. Vue常见性能优化

- 自定义的事件以及DOM事件，要及时销毁，否则会造成内存泄漏；
- 合理使用异步组件；
- 合理使用 keep-alive；
- 组件中 data 的层级不要太深，因为在进行响应式时，需要一层一层递归；
- 使用 vue-loader 在开发环境做模板编译（预编译）；

## 24. Vue中的 router 和 route

- router 是VueRouter的一个对象，Vue.use(VueRouter)，和VueRouter构造函数得到一个router的实例对象，这个对象中是一个全局的对象，他包含了所有的路由包含了许多关键的对象和属性。

  ```js
  $router.push({path:'home'});
  $router.replace({path:'home'});
  ```

- route 对象是一个路由对象，每一个路由都会有一个route对象。

  ![image-20210411223203930](https://i.loli.net/2021/04/11/Js6kFjGbnpyhCW2.png)

### （1） 组件路由传参

- **参数匹配**：

  ```js
  //直接调用$router.push 实现携带参数的跳转
  this.$router.push({
      path: `/particulars/${id}`,
  })
  ```

  **路由配置如下**

  ```js
  {
      path: '/particulars/:id',
      name: 'Particulars',
      component: Part
  }
  ```

  ​		很显然，需要在path中添加/:id来对应 $router.push 中path携带的参数。在子组件中可以使用来获取传递的参数值。

  ```
  this.$route.params.id
  ```

- **params**:

  ```js
  this.$router.push({
      name: 'Describe',
      params: {
      	id: id
      }
  })
  ```

  **路由配置如下**

  ```
  {
      path: '/describe',
      name: 'Describe',
      component: Describe
  }
  ```

- **query：**父组件：使用path来匹配路由，然后通过query来传递参数。这种情况下 query传递的参数会显示在url后面?id=？

  ```js
  this.$router.push({
  	path: '/describe',
  	query: {
  		id: id
  	}
  })
  ```

  **路由配置如下**

  ```
  {
      path: '/describe',
      name: 'Describe',
      component: Describe
  }
  ```

## 25. Vuex的五大属性

- state

- mutations

- actions

- getters：从store派生出的状态

  ```js
  getters: {
  	// 单个参数
  	countDouble: function(state){
  		return state.count * 2
  	},
  	// 两个参数
  	countDoubleAndDouble: function(state, getters) {
  		return getters.countDouble * 2
  	}
  }
  ```

- modules：将多个store分隔岛模块中去，每个模块拥有自己的stete、mutations、action、getter和modules。

  ```js
  const moduleA = {
    state: { ... },
    mutations: { ... },
    actions: { ... },
    getters: { ... }
  }
  
  const moduleB = {
    state: { ... },
    mutations: { ... },
    actions: { ... }
  }
  
  const store = new Vuex.Store({
    modules: {
      a: moduleA,
      b: moduleB
    }
  })
  
  store.state.a // -> moduleA 的状态
  store.state.b // -> moduleB 的状态
  ```

## 26. 不使用trim()去除首尾空格

​		使用正则表达式匹配替换。

```js
s.replace(/^\s+/, '').replace(/\s+$/, '');
```

## 27. WebSocket协议

### （1） 特点

- 与http协议有良好的兼容性
- 建立在TCP之上，和http一样属于应用层协议
- 数据格式轻量，性能开销小，通信高效
- 可以发送文本，也可以发送二进制
- 没有同源限制，可以与任意服务器通信

### （2） Vue-socket.io

- 安装  npm install vue-socket.io --save

- 引入到 vue-cli 中。import VueSocketIO from 'vue-socket.io'

- 使用插件

  ```js
  Vue.use(new VueSocketIO({
      debug: true,
      connection: 'http://metinseylan.com:1992',  //
  }))
  ```

- 组件中使用

  ![image-20210412161909766](https://i.loli.net/2021/04/12/ZGIwOsFu7mEH2hz.png)

## 28. V8内存管理和垃圾回收机制

​		V8 将内存堆区分为两类：新生代内存空间和老生代内存空间，新生代内存空间主要用来存放存活时间较短的对象，老生代内存空间主要用来存放存活时间较长的对象。对于垃圾回收，新生代和老生代有各自不同的策略。

### （1） 副垃圾回收器（Minor GC）

​		副垃圾回收器用于对新生代进行垃圾回收。它把新生代分为两个部分，那就是空闲区和对象区。加入新对象时，该对象会被存储到对象区。当对象区满了以后，副垃圾回收器执行一次垃圾回收，将活动对象有序的放入空闲区，清空对象区，然后将对象区和空闲区对调。

​		如果，某个对象在副垃圾回收器中经历了两轮还没有被回收，则它将被放入老生代，又主垃圾回收器处理。

### （2） 主垃圾回收器（Major GC）

​		主垃圾回收器处理老生代。老生代中的对象来源有两个，一是本身比较大的对象，直接放入老生代，二时由新生代升级过来。主垃圾回收器使用**标记清除和标记整理算法**清理垃圾和整理内存。

## 29.  UDP和TCP报文正文的最大长度

​		MTU（Maximum Transmission Unit）。它是指一种通信协议的某一层上面所能通过的最大数据包大小（以字节为单位）。最大传输单元这个参数通常与通信接口有关（网络接口卡、串口等）。

​		由于网络接口卡的制约，MTU被限制在1500字节，这个长度值得是数据链路层的数据区，大于这个数值的分组可能被分片。在网络层，IP数据报的包头是20字节，UDP最小包头是8字节，TCP最小包头是20字节。因此，UDP报文最大长度为1500-20-8 = 1472字节，TCP报文正文最大长度1500-20-20 = 1460字节。

## 30. ES6 中 static 方法怎么用 ES5 表示

​		类静态方法属于类本身，对象的实例是找不到的。

```js
class person {
    constructor (name) {
        this.name = name;
    }
    call () {
        return "I\'m" + this.name;
    }
    static isPerson() {		// 静态方法
        return true;
    }
}

// ES5
function person(name) {
    this.name = name;
}
person.prototype.call = function(name) {
    return "I\'m" + this.name;
}
// 静态方法，这个方法该对象的实例是找不到的
person.isPerson = function() {
    return true;
}
```

## 31. ICMP和IGMP

​		它们都是网络层的协议。

​		ICMP是网际控制包协议，它的功能是**差错报告和查询**，IP不能提供差错控制和辅助机制（如：主机的管理和查询），因此ICMP承担了此任务。主要应用：ping，traceroute。

​		IGMP是网际组管理协议，它是TCP/IP 协议族中负责IP 组播成员管理的协议，用来在IP主机和与其直接相邻的组播路由器之间建立、维护组播组成员关系。组播使用了D类IP地址。

## 32. 一个服务器可以建立多少个TCP连接

​		对于IP数据包包头，含有源IP和目的IP，其中作为服务器的一个IP，可以产生2^32个连接，而端口号可以有2^16个，因此总共可以产生2^48个连接。如果有n个虚拟IP，则一台服务器可以产生n×2^48个连接。

## 33. HTTP2.0的优点

- **消息推送**：服务器可以根据文件之间的依赖关系，直接将相关的文件推送给客户端，而不需要客户端再次请求这些文件。

- **多路复用**：Chrome浏览器最多支持6个TCP连接，HTTP2.0在一个TCP连接上连续传输，而不像以前串行传输。
- **首部压缩**：使用HPack算法，维护头部的映射表，将头部缓存起来，而不是每次都传输冗长的头部。
- **Stream优先级**：将资源根据重要性赋予优先级，按优先级传输资源。

## 34. 超链接的css属性排列问题

a:link{ }
a:visited{ }
a:hover{ }
a:active{ }

超链接被点击访问过后，hover和active将不再起作用，属性排列顺序应为L-V-H-A。

## 35. TCP拥塞控制

- 慢开始
- 拥塞避免
- 快重传
- 快恢复

![image-20210415131119462](https://i.loli.net/2021/04/15/SBuQDc5O3jibvdo.png)

## 36. html的表单项

```html
<form>
    <input type='text'></input>
	<input type='password'></input>
	<textarea cols='30' rows='10'></textarea>
	<input type='radio' name='sex' value='male'/>男
	<input type='radio' name='sex' value='femal'/>女<br>
	<input type='checkbox' name='hobby' value='s'/>唱
	<input type='checkbox' name='hobby' value='d'/>跳
	<input type='checkbox' name='hobby' value='r'/>rap
	<input type='checkbox' name='hobby' value='b'/>篮球<br>
	<select name="major">
        <option value="cs">计算机科学</option>
        <option value="se" selected>软件工程</option>
        <option value="ne">网络工程</option>    
	</select>
	<input type='submit'></input>
	<input type='reset'></input>
	<button>验证</button>
</form>
```

## 37. 监测网站的性能

​		window.performance.getEntries();

## 38. script标签的defer和async

![image-20210416124603550](https://i.loli.net/2021/04/16/BbTuaGzAeKLj4WR.png)

## 39. defineProperty中的可配置项

```js
Object.defineProperty(o, "b", {
    value: 'bbbb',
	get() { return bValue; },
	set(newValue) { bValue = newValue; },
	enumerable : true,	
	configurable : true,
    writable: true
});
```

- enumerable：是否可枚举，即可否用 for…in 和 Object.keys() 获取到该属性；

- writable：是否可被重新赋值；

- configurable：是否可以删除目标属性或是否可以再次修改属性的特性。设置为true可以被删除或可以重新设置特性；设置为false，不能被可以被删除或不可以重新设置特性。默认为false。

  这个属性起到两个作用：

  1. 目标属性是否可以使用delete删除
  2. 目标属性是否可以再次设置特性

## 40. 词法作用域

​		词法作用域，就是静态作用域，无论函数在哪里调用，也无论如何被调用，它的词法作用域都值由函数声明时所处的位置决定的。编译的词法分析阶段基本能够知道全部的标识符在哪里，以及它们是如何声明的，从而可以预测在执行的过程中如何对它们进行查找。JS使用的是词法作用域，大部分的语言使用的都是词法作用域。

​		**动态作用域**，动态作用域并不关心函数和作用域是如何声明以及在任何处声明的，只关心它们从何处调用。换句话说，作用域链是基于调用栈的，而不是代码中的作用域嵌套。	

## 41. Promise/A+规范，实现Promise

- 基本框架，构造函数接受一个参数executor函数，这个函数又有两个参数，分别是resolve和reject。函数同步执行要放在try…catch中，否则无法进行错误捕获。
- 添加状态机，三个状态 pending、fulfilled、rejected。分别在resolve函数和reject函数中进行状态转换。
- 添加then方法，then方法接受两个参数 onFulfilled 和 onRejected。
- 添加异步队列，调用then时，将方法加入到队列中，在 resolve 和 reject 调用时依次执行队列中的方法。
- 为了链式调用，要在then中的方法调用后返回一个新的promise。

## 42. cookie有哪些属性

- name和value，是一个键值对；
- domain，决定了cookie在哪个域是有效的，也就是决定在该域发送请求时是否携带此cookie；
- path，cookie的有效路径；
- expires/max-age，都表示cookie的有效期，expires是该cookie被删除的时间戳，max-age是cookie的有效时间长度，单位是秒；
- size，表示该cookie的大小。
- http-only，表示是否可以通过脚本设置或读取cookie，document.cookie；
- secure，是否仅被使用安全协议传输。安全协议。安全协议有HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。

## 43. cookie和session的区别

​		它们都是常用的会话跟踪技术。Cookie通过在客户端记录信息确定用户身份，Session通过在服务端记录信息确定用户身份。

- 一个存放在浏览器端，一个放在服务器上；
- cookie不安全，容易被他人获取；
- 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。(Session对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)；
- 生命周期不同，session的生命周期是用户登录到关闭浏览器页面。

## 44. JS六种继承方式

- 原型链继承：将父类的实例作为子类的原型，是非常纯粹的继承关系，实例既是子类实例，又是父类实例。而且父类新增的原型方法和属性子类都能访问。

  **缺点：**不能实现多继承，所有的实例共享原型对象，创建子类实例时无法向父类构造函数传参。

- 构造继承：子类构造函数中执行父类构造函数，需要绑定this指向到子类对象。这样每个子类对象不共享父类对象，而且可以向父类函数传参，并且可以实现多继承。

  **缺点：**实例并不是父类的实例，而且只能继承父类的实例属性和方法，无法继承它原型上的属性和方法。每个子类都有父类实例函数的副本，影响性能。

- 实例继承：子类构造函数中new一个父类对象，为它添加新特性，然后返回这个新对象。特点是不限制调用的方式。

  **缺点：**实例是父类的实例而不是子类的实例，不支持多继承，因为只能返回一个对象。

- 拷贝继承：子类构造函数中new一个父类对象，然后for…in遍历对象，将它的属性拷贝到子类原型中去。特点是支持多继承。

  **缺点：**效率低，内存占用高，而且无法获取父类的不可枚举方法（for…in遍历）。

- 组合继承：结合原型链继承和构造继承，这样父类实例和其原型上的属性和方法都能被继承，它既是子类实例，也是父类实例，也不存在引用共享问题，可传参。

  **缺点：**调用了两次父类构造函数，生成了两份实例，并且子类实例将父类原型屏蔽掉了。

- 寄生组合继承：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点。几乎完美，就是实现较为复杂。

  ```js
  function Cat(name){
    Animal.call(this);
    this.name = name || 'Tom';
  }
  (function(){
    // 创建一个没有实例方法的类
    var Super = function(){};
    Super.prototype = Animal.prototype;
    //将实例作为子类的原型
    Cat.prototype = new Super();
  })();
  ```


## 45. 对象遍历方法

- for…in：遍历对象自身的和**原型链上**的**可枚举属性**（不含Symbol属性）；
- Object.keys()：返回一个数组，包括对象自身的**可枚举属性**（不含原型上的属性和Symbol属性）；
- Object.getOwnPropertyNames()：返回一个数组，包含自身的**所有属性**（包含不可枚举属性，不包含Symbol属性）；

- Reflect.ownKeys()：返回一个数组，包含对象自身的**所有属性**（包含不可枚举和Symbol属性）。

## 46. 深拷贝数组

- 遍历复制，返回复制后的新数组；
- arrCopy  =  arr.slice(0)；
- arrCopy  =  [ ].concat(arr)

## 47. WebSocket和HTTP的区别

- HTTP是单向的协议，即浏览器只能向服务器请求资源，服务器才能将数据传送给浏览器。WebSocket是全双工通信的双向协议，服务端和客户端都可以互相发送消息。

## 48. Vue.delete 和 delete的区别

- delete 操作符：删除对象的 property。如果删除数组中的某项元素，并不会改变数组长度，原来的空位会变成 undefined；

- `Vue.delete` 删除对象的 `property`。如果对象是响应式的，确保删除能触发更新视图。这个方法主要用于避开 `Vue` 不能检测到 `property` 被删除的限制，但是你应该很少会使用它。

## 49. js的Number类型

​		一个Number类型的对象，总共8字节，64位，其中一个符号位，11个指数位，52个分数位，因此整数的可表示范围是 -2^53 ------ 2^53（不含端点）。

## 50. VueRouter路由守卫

- 全局的路由守卫：beforeEach，afterEach；
- 路由独享守卫：beforeEnter，在路由对象中添加这个属性；
- 组件内路由守卫：beforeRouteEnter，beforeRouteLeave，beforeRouteUpdate

## 51. WeakSet，WeakMap

​		和Set、Map差不多，只能存储**对象值**，不能枚举，没有长度，会触发垃圾回收机制，值在没有使用的时候会被自动回收。唯一的作用就是可以用来存储DOM节点，当节点被移除之后，内存被自动回收。

​		正是因为这种自动回收机制，所以它们没有长度，不能遍历。即size属性，entries方法、keys方法、values方法等用于遍历的方法都没有用。

## 52. SVG和Canvas

​		<canvas>和<svg>都是HTML5推荐使用的图形技术，Canvas基于像素，提供2D绘制函数，是一种HTML元素类型，依赖于HTML，只能通过脚本绘制图形；SVG为矢量，提供一系列图形元素（Rect, Path, Circle, Line …），还有完整的动画，事件机制，本身就能独立使用，也可以嵌入到HTML中。

​		**适用场景**

​		Canvas提供的功能更原始，适合像素处理，动态渲染和大数据量绘制。SVG功能更完善，适合静态图片展示，高保真文档查看和打印的应用场景。

## 53. 普通函数和箭头函数的区别

- this的指向问题，箭头函数不绑定this，会捕获其所在的上下文环境中的this值作为自己的this；
- 箭头函数相当于匿名函数，因此不能当作构造函数，用来new一个对象；
- 箭头函数不绑定arguments，取而代之用 …rest 实现；
- 箭头函数没有显式的原型属性，即没有prototype。

## 54. Proxy和Object.defineProperty

- Object.defineProperty，value和get互斥，writable和set互斥，enumerable，configurable。只是对一个对象的一个属性进行定义，想要劫持整个对象就需要遍历调用。而且不能监听到数组的变化，可劫持的操作只有get和set。但是兼容性较好。
- Proxy：可以直接监听整个对象，可以监听到数组的变化，可劫持的操作包括get，set，defineProperty等13种操作。但是操作的时候并不是对元对象进行操作，而是对那个Proxy实例进行操作。兼容性较差。

## 55. 发布订阅模式/观察者模式

​		Vue实现响应式的时候，使用了观察者模式，

​		Vue中的事件绑定，就是发布订阅模式。

​		观察者模式内部是包含发布订阅的（Watcher和Dep），观察者模式中的观察者和被观察者是存在关联的，而发布者订阅模式中的订阅者和发布者没有关联，它们只与调度中心交互。

## 56. Vue.use()

​		通常是用来注册全局插件的，它实际上是一个封装，底层是调用导入的插件的install方法或者plugin本身是个函数的话就调用plugin函数。在引入的插件的源码中的方法中会有Vue.component()方法来注册组件，Vue.use()相当于间接地调用它们。

## 57. Promise.all 的实现

```js
Promise.myAll = function(promises) {
    return new Promise((resolve, reject) => {
        const len = promises.length;
        let count = 0, results = new Array(len);
        for (let i = 0; i < len; i ++) {
            Promise.resolve(promises[i]).then(value => {
                results[i] = value;
                if (++ count === len) {
                    return resolve(results);
                }
            }, reason => {
                    return reject(reason);
               }
           )
        }
    });
}
```

## 58. CSS选择器的匹配顺序

​		CSS选择器是从右向左匹配的。

```css
.mod-nav h3 span {font-size: 16px;}
```

​		首先找到所有的span，然后沿着span的父元素查找h3，中途找到了符合匹配规则的节点就加入结果集；如果直到根元素html都没有匹配，则不再遍历这条路径，从下一个span开始重复这个过程（如果有多个最右节点为span的话）。

​		从右向左的匹配在第一步就筛选掉了大量的不符合条件的最右节点（叶子节点）；而从左向右的匹配规则的性能都浪费在了失败的查找上面。

## 59. Babel的原理

​		babel是一个转译器，感觉相对于编译器compiler，叫转译器transpiler更准确，因为它只是把同种语言的高版本规则翻译成低版本规则，而不像编译器那样，输出的是另一种更低级的语言代码。

​		但是和编译器类似，babel的转译过程也分为三个阶段：**parsing、transforming、generating**，以ES6代码转译为ES5代码为例，babel转译的具体过程如下：

​		ES6代码输入——> babylon进行解析——>获得AST——>plugin用babel-traverse对AST树进行遍历转译——>得到新的AST——>用babel-generator通过AST树生成ES5代码。

## 60. 微信红包的随机算法是怎么实现的

​		为了避免有很大的并发量，每个红包的大小并不是提前分好的，而是每抢一个红包计算一次。每次的红包大小的范围应该在 0.01元到剩余remainMoney - remainNum * 0.01之间。其中，remainMoney是剩余钱数，remainNum是剩余红包数量。可以利用这个区间进行随机计算。

## 61. 网站图片太多了怎么优化

- 懒加载（延迟加载）；
- 减小图片：压缩图片，选择更小的图片格式；
- 选择合适的图片尺寸（响应式图片）：pc端和移动端都会有很多不同尺寸的图片，根据屏幕分辨率实现不同的图片选择；
- 减少HTTP请求：CSS图片可以使用雪碧图，然后使用背景的background-img，background-repeat，background-position组合进行定位。
- 使用SVG技术来代替图片：SVG是使用XML描述二维图形和绘图程序的语言；
- 使用canvas来绘制图形；

## 62. gzip算法的原理

​		gzip对于要压缩的文件，首先使用Lz77算法的一个变种 进行压缩，然后对得到的结果再使用Huffman编码的方法进行压缩。

​		Lz77算法将长字符串（也称为短语）编码成短小的标记，用小标记代替字典中的短语，从而达到压缩的目的。也就是说，它通过用小的标记来代替数据中多次重复出现的长串方法来压缩数据。其处理的符号不一定是文本字符，可以是任意大小的符号。

## 63. 如何判断socket的连接状态

​		在Vue中使用vue-socket时，对于socket对象，有一个io属性，io属性会有connected和disconnected的属性，表示是否连接。

## 64. TCP、UDP、IP报头有些什么字段

- TCP：源端口，目的端口，序号，确认号，检验和，窗口大小……
- UDP：源端口，目的端口，报文长度（，检验和）。
- IP：版本，首部长度，总长度，协议，首部检验和，源IP，目的IP……

## 65. 如何在不影响主程序的情况下完成一些JS任务

- Web Worker
- requestAnimationFrame
- Service Worker
- Chrome浏览器的插件
- flash

## 67. 线程之间共享哪些资源

- 线程之间共享的资源：
  - **堆**：由于堆是在进程空间中开辟出来的，所以它是理所应当当共享的，因此new出来的都是共享的；
  - **全局变量**：它与具体某一函数无关，也与特定的线程无关，因此也是共享的；
  - **静态变量**：其存放位置与全局变量一样;
  - **文件等公共资源**：使用公共资源的线程必须同步，win32提供了几种同步资源的方式，包括信号量、临界区、事件和互斥体。
- 独享资源：
  - 栈是独享的；
  - 寄存器是独享的。

## 68. 进程间的通信方式

- 管道（pipe）：管道是一种半双工通信方式，数据只能单向流动，只能在具有亲缘关系的进程之中使用（通常为父子进程）；
- 具名管道（FIFO）：也是半双工通信，允许在无亲缘关系进程通信；
- 消息队列（Message Queue）：消息队列是消息的链表，存放在内核并由消息队列标识符标识；
- 共享存储（Share Memory）：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。
- 信号量（Semaphore）：信号量是一个计数器，可以用来控制多个进程对共享资源的访问。
- 套接字（Socket）：Socket是应用层与TCP/IP协议族通信的中间软件抽象层,它是一组接口。

## 69. Vue3做了哪些优化

- 重写虚拟DOM：更新vdom比较的时候跳过了不必要的条件分支；
- 优化插槽生成：在当前的 Vue 版本中，当父组件重新渲染时，其子组件也必须重新渲染。 使用 Vue 3 ，可以单独重新渲染父组件和子组件。
- 静态树提升：Vue3编译器能够检测到什么是静态组件，然后将其提升，降低渲染成本。提升的组件能跳过整个树形结构patch的过程；
- 静态属性提升： Vue 3 将跳过不会改变节点的打补丁过程；
- 事件监听缓存： 以前是给每个事件的事件处理函数开辟空间，即使有相同的事件处理函数；
- 基于Proxy的观察者机制；
- Tree Shaking：在打包构建的过程中移除没有引用到的代码，这些代码可以称为dead code。TreeShaking 比较依赖于 ES6 模块系统的静态结构特性，比如 `import` 和 `export`。

## 70. 前端离线缓存

​		离线缓存是HTML5的新特性之一，简单理解就是第一次加载之后将数据缓存，在没有清除缓存的前提下，下一次没有网络也可以加载，用在静态数据的网页或者游戏上较为合适。要想使web应用程序在离线状态的时候也能正常工作，就必须把所有构成web应用程序的资源文件，如HTML文件、CSS文件、JavaScript脚本文件等放在本地缓存中，当服务器没有和Internet建立连接时，也可以利用本地缓存中的资源文件正常运行web应用程序。

​		使用方式是编写manifest文件并引入网页。在manifest文件中，CACHE下面的都是缓存的文件，NETWORK表示每次都从网络请求，FALLBACK：指定的文件若是找不到，会被重定向到新的地址。注意，第一行必须是”CACHE DMANIFEST”文字，以把本文件的作用告知浏览器，即对本地缓存中的资源文件进行具体设置。

## 71. Service Worker

​		它是一个服务器和浏览器之间的中间人角色，如果网站注册了service worker，那么它可以拦截当前网站的所有请求并进行判断（需要编写相应的判断程序）。如果需要向服务器发起请求的就转给服务器，如果可以直接使用缓存的就直接返回缓存不再转给服务器。从而大大提高浏览体验。

​		它是基于web worker的，是一个独立于JavaScript主线程的独立线程，在里面需要消耗大量资源的操作不会堵塞主线程，而且它在web worker的基础上增加了离线缓存的能力，还可以访问cache和indexedDB等。

## 72. Vue中常见的内存泄漏场景

- 自定义指令给元素绑定事件，但是没有解绑事件；

- v-if导致的内存泄漏：在某个Vue实例中保存着子组件DOM元素的引用，那么销毁父组件的时候，这个DOM元素并没有被销毁；
- 对window/body添加的事件，在组件销毁的时候没有移除；
- 组件之间传值使用事件总线，添加$on的一方没有将事件$off。

## 73. Canvas优化

- **离屏渲染**：在离屏canvas上预渲染相似的图形或重复的对象，在离屏canvas上绘制好一整块图形，绘制好后在放到视图canvas中，适合每一帧画图运算复杂的图形；
- **分层画布**：多个相互重叠的canvas根据变化的程度分开渲染，越复杂的场景越适合；
- **一次性绘制**：绘制的操作性能开销较高，可以创建一个包含所有线条的路径，然后通过单个绘制路径调用进行绘制；
- **使用requestAnimationFrame执行动画**：canvas动画的本质是不断地擦除和重绘；
- **清除画布**：有三种方法清除，这三种方法的性能依次提高。
  - context.fillRect()	重新填充canvas背景色，相当于清除；
  - context.clearRect()    清除矩形；
  - canvas.width = canvas.width    给画布重新设置宽高的时候，自动清除。

- **减少调用canvas的api**：能使用css处理的样式不要调用它的api
- **尽可能地使用计算代替canvas的渲染**：通常，渲染比计算的开销要大得多（3-4个量级）。

## 74. Canvas的状态机

​		context 是一个状态机。你可以改变 context 的若干状态，而几乎所有的渲染操作，最终的效果与 context 本身的状态有关系。比如，调用 strokeRect 绘制的矩形边框，边框宽度取决于 context 的状态 lineWidth ，而后者是之前设置的。

## 75. TSDB时序数据库原理

### （1） 传统数据库有什么问题

​		在大数据的环境下，传统的数据库（关系型数据库）有以下的限制：

- 数据复杂，关系模式难以应付；
- 传统数据库讲究数据的ACID，但是现在很多场景并不需要；
- 传统数据库底层使用B/B+树来实现索引，在大数据环境下性能存在问题。

### （2） CAP定理

- C：Consistency，所有用户同一时间看到的数据是一样的；
- A：Availability，所有用户在任意时间可读写数据库；
- P：Partition Tolerance，不同物理地址的同一份数据是相同的。

### （3） LSM树（Log-Structure Merge-tree）

- 日志式数据：增量式写入，线性（返回式查找）。写入性能高，读取性能低，但是对于大数据环境，写入的数据量远大于读取的数据量。
- 如何写入：先将数据写入内存，在内存中保持有序，当达到指定大小，再将数据一次性写入磁盘，写入磁盘中的数据和在内存中的数据都是有序的，因此是可以做二分搜索的。
- 如何更改或者删除：并不直接修改插入过的数据，而是新插入数据，并且添加修改或者删除的标识符，写入磁盘。
- 定期合并（merge）：定期磁盘中写入的块两两合并，如果碰到了更新或删除的标记就执行 更新或者删除。

## 76. 碰撞检测原理

- BSP（Binary Spacing Partition  二叉空间分割）树：能在早期排除大量的多边形，所以在最后我们仅仅是对少数面进行碰撞检测。用找出两个物体间的分隔面的方法适合于判断两个物体是否相交。如果分隔面存在，就没有发生碰撞。因此我们递归地遍历world树并判断分割面是否和包围球或包围盒相交。还可以通过检测每一个物体的多边形来提高精确度。检测运算使用了笛卡尔平面等式；
- 八叉树和四叉树：快速排除不可能相撞的物体，只在小范围以内查找或检测具体的某个物体。
- AABB（Axis-align Bounding Box）包围盒算法：对物体创建一个规则形状，使用简单的规则就能判断是否碰撞；

## 77. Canvas和DOM绘制

​		（详情：https://zhuanlan.zhihu.com/p/143829714）

​		浏览器是如何将我们所想要的效果转化成为浏览器的渲染。这种转换的本质在于两种模式：驻留模式和快速模式。理解这两种模式可以帮助我们理解何时使用DOM，何时使用canvas。

- **驻留模式**（DOM）：浏览器通过图形化API完成标签或代码同页面渲染的转换。这些api会接受指定的内容，并将生成一个存在与内存中的模型。这个模型关联着浏览器中实际呈现的内容。只要创建这个模型，这个模型最终将会转换成晦涩的浏览器渲染指令渲染页面。（译者：这些模型指的是渲染树。）

  ![image-20210430190711070](https://i.loli.net/2021/04/30/dHiITg9cJZGXoRB.png)

- **快速模式**（canvas）：在快速模式中，开发者需要关心很多逻辑。不仅需要指定需要渲染什么东西，而且还要自己维护一个模型。并且开发者必须自己通过渲染指令完成实际的渲染。图形化API在这里并不会做太多事情，它只会将渲染指令发送到浏览器，并执行。在html中，当使用canvas元素的时候，浏览器就会采取快速模式。

  ![image-20210430190835927](https://i.loli.net/2021/04/30/BQ2FbtfLCqDOeJj.png)

- 什么时候选择：

  ​		当页面图像复杂、或者有弱人机交互的动画、或者需要像素级别图像处理的时候，使用canvas，其他时候使用DOM来处理。

## 78.  nextTick的实现原理

​		在下次DOM更新循环结束之后执行回调。nextTick主要使用了宏任务和微任务，根据执行环境分别使用了Promise、MutationObserver、setImmediate，如果都不行，则使用setTimeout。

## 79. 模型描边原理

- **通过法线延伸**：进行两次渲染，第一次把模型顶点沿着法线方向延伸一定的距离，并且只显示描边的颜色，第二次渲染模型，挡住一部分第一次渲染的部分，最终留下细长的外边。
- **深度偏移**：同一个物体在透视投影下，离相机越近，其大小就越大。因此，进行两次渲染，第一次只渲染描边，并在z方向偏移，第二次渲染物体本身。

## 80. 函数中的this指向问题

- 函数执行，首先看函数名前面是否有“.” ，有的话， “.”前面是谁，this就是谁；没有的话this就是window；
- 立即执行函数的this永远指向window；
- 给元素的某一事件绑定方法，当事件触发的时候，执行对应的方法，方法中的this是当前元素。

## 81. ES7、ES8新特性

- ES7:

  - 求幂运算符 ** ：Math.pow(3, 2) === 3 ** 2；
  - Array.prototype.includes()；

- ES8：

  - async await：其实是Generator的一个语法糖

  - Object.entries()：将某个对象的可枚举属性按照二维数组的方式返回，如果是数组则下标作为键值；

  - Object.value()：返回对象中的值，结果是一维数组；

  - 字符串的填充padStart()、padEnd()。

    ```js
    'react'.padStart(10, 'm')      //'mmmmmreact'
    'react'.padEnd(10, 'm')       //' reactmmmmm'
    'react'.padStart(3, 'm')     // 'vue'
    ```

    

## 82.  前端数据怎么做持久化

> ​		vuex可以进行全局的状态管理，但刷新后刷新后数据会消失。

- localStorage存储数据
- vuex-persistedstate插件
- 使用vue-cookie插件操作cookie

## 83. instanceOf的原理

- 用于判断某个实例是否属于某构造函数

- 在继承关系中用来判断一个实例是否属于它的父类型或者祖先类型的实例

  ​	说白了，只要右边变量的 prototype 在左边变量的原型链上即可。因此，instanceof 在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 prototype，如果查找失败，则会返回 false。

## 84. HTTP协议有什么特点，解决了什么问题

- **无连接**：每次连接只处理一个请求，处理完后断开连接。这么做的原因是HTTP协议产生于互联网，服务器要服务很多的客户，如果进行长时间的连接会导致通道长时间空闲，无端占用资源；
- **无状态**：指协议对于事务的处理没有记忆能力，服务器不知道客户端是什么状态，这是由无连接特点造成的。每个请求都是独立的，状态的保持是由cookie和session实现的；
- 支持客户/服务器模式；
- 简单快速：客户端向服务器请求服务的时候只需要传送请求的方法和路径；
- 灵活：HTTP允许传输任意类型的数据对象，正在传输的类型由Content-Type加以标记。

## 85. Web应用的缓存

​		在Web应用中使用到缓存的地方很多，主要有浏览器缓存、页面缓存、服务器缓存、数据库缓存等等。缓存的作用主要有：（1）加快页面的打开速度，（2）减少网络带宽消耗，（3）降低服务器的压力等等；

- 浏览器缓存：使用HTTP协议头和HTML文件中的<meta>标签定义；
- 页面缓存：将动态页面直接生成为静态的页面保存在服务端，用户调取相同页面的时候，将静态页面直接下载到客户端，不需要通过程序运行和数据库访问。如果没有缓存则再执行后端程序取数据等；
- 数据库缓存：一般由数据库提供，可以对表建立高速缓存。数据库中可能多次执行相同的sql，可以在内存划分一块专门存放最近执行的查询，这就是缓存。

## 86. 协程

> ​		**协程是一种用户态的轻量级线程**，协程的调度完全由用户控制。从技术的角度来说，“协程就是你可以暂停执行的函数”。协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操作栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。

- 进程和协程的区别：
  - 一个线程可以拥有多个协程；
  - 线程进程都是同步机制，协程则是异步；
  - 协程能保留上一次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态。
  - 线程是抢占式的，协程是非抢占式的，所以需要用户自己释放使用权来切换到其他的协程，同时一时间其实只有一个协程拥有运行权，相当于单线程的能力；
  - 协程并不是取代线程, 而且抽象于线程之上, 线程是被分割的CPU资源, 协程是组织好的代码流程, 协程需要线程来承载运行, 线程是协程的资源, 但协程不会直接使用线程, 协程直接利用的是执行器(Interceptor), 执行器可以关联任意线程或线程池, 可以使当前线程, UI线程, 或新建新程.。
  - 线程是协程的资源。协程通过Interceptor来间接使用线程这个资源。

## 87. 队头阻塞以及解决方法

- **TCP对头阻塞**：队头阻塞发生在一个TCP分段的丢失，导致其后续分节不按序到达接收端的时候。该后续分段被接收端一直保持直到丢失的第一个分段被重新传送到接收端为止。这是TCP的机制决定的，因为TCP是一个按序传输的链条，所以如果其中一个点丢失了，链路上之后的内容就都需要等待。

- **HTTP阻塞**：HTTP1.1允许在持久连接上可选的请求管道，这是相对于keep-alive连接的又一性能优化。在响应到达之前，可以将多条请求放入队列，当一条请求发往服务器的时候，第二条第三条也能发送。如果一个响应返回延迟了，那么后续的响应都会被延迟，直到对头的响应到达。

  > ​	管道化的限制：
  >
  > - 管道化的要求服务端按照请求发送的顺序返回响应（FIFO），原因是HTTP请求和响应没有序号标识，无法将乱序的响应与请求关联起来；
  > - 客户端需要保持未收到响应的请求，当连接意外中断，需要重新发送这部分请求；
  > - 只有幂等的请求才能进行管道化，也就是只有GET和HEAD请求才能管道化，否则可能出现以外的结果。

- 如何解决队头阻塞：
  - 对于HTTP1.1的管道化导致的请求、响应级别的队头阻塞，可以使用HTTP2.0不使用管道化的方式，而是引入帧、消息和数据流等概念。每个请求/响应被称为消息，每个消息都被拆分成若干个帧进行传输，每个帧都分配一个序号。每个帧在传输是属于一个数据流，而一个连接上可以存在多个流，各个帧在流和连接上独立传输，到达之后在组装成消息，这样就避免了请求/响应阻塞。
  - 对于TCP队头阻塞，是由于TCP的特点决定的，无法避免。只能舍弃TCP，比如Google的quic协议，在UDP的基础上实现了可靠传输，UDP是面向数据报的，数据报之间不会有阻塞约束。

## 88. IFC

> - FC的含义就是Fomatting Context。它是CSS2.1规范中的一个概念。
> - 它是页面中的一块渲染区域。而且有一套渲染规则，它决定了其子元素将怎样定位。以及和其它元素的关系和相互作用。
> - BFC和IFC都是常见的FC。分别叫做Block Fomatting Context 和Inline Formatting Context。

- 布局规则如下：
  - 内部的盒子会在水平方向，一个个地放置；
  - IFC的高度，由里面最高盒子的高度决定；
  - 当一行不够放置的时候会自动切换到下一行；
- 用处
  - 水平居中：当一个块要在环境中水平居中时候，设置其为inline-block则会在外层产生IFC，通过text-align:center则可以使其水平居中。
  - 垂直居中：创建一个IFC，用其中一个元素撑开父元素的高度，然后设置其vertical-align:middle,其他行内元素则可以在此父元素下垂直居中。


## 89. 简单请求和预检请求

​		总体来说，不会对服务器产生副作用的请求称为简单请求。

- 简单请求满足以下两个条件：HEAD请求、GET请求以及部分POST请求。这一部分的POST请求是指Content-type为text/plain、multipart/form-data或者application、x-www-form-unlencoded。
- 非简单请求是除了简单请求的请求，每次发送非简单请求的时候，都会向服务器先发送一个不带body的预检请求（使用options方法），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 [Cookies ](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)和 HTTP 认证相关数据）。

## 90. Webpack externals

​		webpack配置external后，可以不处理应用的某些依赖库，使用externals配置后依旧可以在代码中使用CMD、AMD或者window/global全局的方式访问。

​	步骤：

- 在HTML中引入cdn第三方库
- 在webpack中配置externals
- 在js中引用

## 91. Vue和React

### （1） 相似之处

- **Virtual DOM**：一个映射真实DOM的JavaScript对象，如果需要改变任何元素的状态，那么是先在Virtual DOM上进行改变，而不是直接改变真实的DOM。当有变化产生时，一个新的Virtual DOM对象会被创建并计算新旧Virtual DOM之间的差别。之后这些差别会应用在真实的DOM上。 
- **组件化**：将应用分拆成一个个功能明确的模块，每个模块之间可以通过合适的方式互相联系。
- **props**：是properties的简写，允许父组件向子组件传递数据。
- **构建工具**：都有自己的构建工具，可以用来快速搭建环境。
- **都只专注UI层**：两个框架都只关注UI，而其他的功能如路由、状态管理等都交给同伴框架进行处理。

### （2） 主要区别

- **模板和JSX**：Vue使用HTML模板，比较贴合传统的前端开发者。React使用JSX来完成DOM的编写，

- **状态管理和对象管理**：React中，state状态是核心概念，它是不可手动更改的，只有通过setState才能更新状态，并且更新UI。在Vue中数据通过data等进行管理。
- **数据流**：Vue设计理念是双向绑定，React提倡数据单向流动。

- **框架类型**：Vue是MVVM框架，其来源于MVC模式。React是组件化框架，是由后端组简化演化来的。

## 92. HMR

### （1） 启用

​		只需要在 `webpack.config.js` 中添加 `devServer` 选项，并设置 `hot` 值为 `true` ，并使用`HotModuleReplacementPlugin` 和 `NamedModulesPlugin` （可选）两个 Plugins ：

```javascript
devServer: {
	hot: true,   // 启动模块热更新 HMR
    open: true,  // 开启自动打开浏览器页面
},
plugins: [
  	new webpack.NamedModulesPlugin(),
    new webpack.HotModuleReplacementPlugin()
]
```

​		然后使用webpack-dev-derver打开项目。

### （2） 原理

​		https://segmentfault.com/a/1190000022485386

​		https://www.codercto.com/a/90387.html

- 修改了一个或多个文件。

- 文件系统接收更改并通知Webpack。

- Webpack重新编译构建一个或多个模块，并通知HMR服务器进行了更新。

- HMR Server使用websockets通知HMR Runtime需要更新。（HMR运行时通过HTTP请求这些更新。）

- HMR运行时再替换更新中的模块。如果确定这些模块无法更新，则触发整个页面刷新

## 93. CSS选择器优先规则

​		CSS优先级：是由四个级别和各级别的出现次数决定的。

　　四个级别分别为：行内选择符、ID选择符、类别选择符、元素选择符。

　　优先级的算法：

　　每个规则对应一个初始"四位数"：0、0、0、0

　　若是 行内选择符，则加1、0、0、0

　　若是 ID选择符，则加0、1、0、0

　　若是 类选择符/属性选择符/伪类选择符，则分别加0、0、1、0

　　若是 元素选择符/伪元素选择符，则分别加0、0、0、1

　　算法：将每条规则中，选择符对应的数相加后得到的”四位数“，从左到右进行比较，大的优先级越高。　

**注意**：

​		①、!important的优先级是最高的，但出现冲突时则需比较”四位数“;

　　②、优先级相同时，则采用就近原则，选择最后出现的样式;

　　③、继承得来的属性，其优先级最低;

　　!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性

　　*css选择器使用强烈建议采用低权重原则，利于充分发挥css的继承性，复用性，模块化、组件化。　

## 94. JS中的sort实现

​		**不同浏览器的sort实现方法不一样：**

- Chrome：对于长度<=10的数组使用插入排序，>10的数组使用快速排序；
- Firfox：归并排序；
- Safari：默认使用的桶排序，如果 `sort` 传入的自定义函数作为参数，就是用归并排序（稳定排序算法）；
- Edge(IE 9.0+)：快速排序。

## 95. for...in / for...of

- for...in：语句用于对数组或者对象的属性进行循环操作。循环中的代码每执行一次，就会对数组的元素或者对象的属性进行一次操作。语句以任意顺序遍历一个对象的可枚举属性。对于每个不同的属性，语句都会被执行**数组索引只是具有整数名称的枚举属性，并且与通用对象属性相同。不能保证`for ... in`将以任何特定的顺序返回索引。`for ... in`循环语句将返回所有可枚举属性，包括非整数类型的名称和继承的那些。**因此尽量不要用for   in访问数组。

- for...of：ES6引入的新的语法，**`for...of`语句在可迭代对象（包括 `Array`，`Map`，`Set`，`String`，`TypedArray`，arguments 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。**

> 总之，for...in 循环主要是为了遍历对象而生，不适用于遍历数组 
>
> for...of 循环可以用来遍历数组、类数组对象，字符串、Set、Map 以及 Generator 对象

## 96. IM的通讯协议

- 对于QQ登录：首先使用HTTP协议，登录，会有一个TCP协议来保持登陆状态。（无论是TCP登录还是UDP登录）；
- 对于聊天消息：采用UDP协议，通过服务器中转的方式，因此在仅聊天的时候IP侦探是无法获取到IP的。腾讯采用上层协议来保障可靠传输，如果客户端使用UDP协议发出消息后，服务器收到该包，需要使用UDP协议发回一个应答包。
- 对于文件和自定义表情：自定义表情是以文字的方式进行传输的。传文件要使用TCP协议。

## 97. 文件下载如何实现

- 第一种，直接下载服务器的资源，使用a标签给href，其他的方法比如form、iframe、location.href、window.open()也一样；

- 前端传参：前端通过发送数据或参数传给后端，再由后端根据接收到的数据生成文件，或根据参数查找出对应文件，最后在响应头中设置，

  `Content-disposition：attachment；filename="fliename.fileType"`

  浏览器接受到这个响应头就会触发下载行为。

- 对于已知文件内容的，比如后端传来的文件里的内容，可以使用`URL.createObjectURL()`下载文件，这个方法支持传入file对象、blob对象或者MediaSource对象（媒体资源）。添加a标签用js触发点击后将a标签删除。

## 98. 解决vuex刷新清除的问题

- 手动：将vuex中的state用sessionStorage存储起来，刷新后还原：

```js
// store的写法
const store = new Vuex.Store({
    state:sessionStorage.getItem('state') ? JSON.parse(sessionStorage.getItem('state')): {
        //id
        skillId:'',
        //技能状态
        checkStatus:''
    }
})

// App.vue中添加
		mounted() {
            window.addEventListener('unload', this.saveState)
        },
        methods: {
            saveState() {
                sessionStorage.setItem('state',JSON.stringify(this.$store.state))
            }
        }
```

- 利用vuex-persistedstate插件

## 99. window.getComputedStyle和style

- 前者只读，后者可读写；

- 获取的范围不一样，前者获取的是最终应用到元素上的样式值，而后者只能获取到元素内联样式的css样式。

  >  因此对于一个光秃秃的元素<p>，getComputedStyle方法返回对象中length属性值（如果有）就是190+(据我测试FF:192, IE9:195, Chrome:253, 不同环境结果可能有差异), 而element.style就是0。

- 前者是个方法，能够获取到元素的伪元素的样式，而后者不行。

```js
var h3 = document.querySelector('h3');
var result = getComputedStyle(h3, ':after').content;
```

## 100. url编码

- 为什么要编码：HTTP协议中参数组件的传输是“key=value”键值对的形式，如果要传输多个参数就需要用“&”符号对键值对进行分隔。如果参数中本身就含有url保留字，服务端就会产生歧义，因此需要对url进行编码来消除歧义；
- 如何编码：在特殊字符的各个字节（16进制）前加上“%”即可。

