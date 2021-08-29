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

## 2. props数据单向流动

​		Vue中的props传递是单向的，也就是说父子组件的prop之间形成的是一个单向下行绑定：**父级 prop 的更新会向下流动到子组件中，但是反过来则不行**。

​		**这么做的原因：**防止子组件更改父组件的状态，组件是可以复用的，假设子组件被复用了n次，当一个组件更改了父组件的状态，其他的子组件跟着就一起改了，引起混乱。正确的做法是将传递过来的props进行转换处理，利用 `computed` 计算属性处理。

## 3. JS和CSS会不会阻塞浏览器解析和渲染

## 4. 页面白屏怎么排查

​		就经验来说，如果页面完全一片空白。极有可能是后端出现问题，并且禁用了错误输出。比如apache2，后端配置无错误输出，并且服务状态500，这个时候页面就是一片空白。
如果是前端导致的，那么极有可能是单页应用异常，因为普通的html输出，哪怕出了一些错误，也不太可能一片空白。
​		排查步骤如下：

- 打开能看到源码和request、response的浏览器，如chrome，查看源码输出，如果做不到，那么使用局域网数据监控软件查看页面输出。查看源码是否有异常，http状态等，本步骤用于检查具体是后端还是前端问题，还是网络问题。
- 如果是后端问题，那么后端查看access log、程序日志，看看是否有问题
- 如果是前端问题，那么根据给出的js异常之类的排查

## 5. overflow:hidden清除浮动的原理

​	如果没有明确设定容器高情况下它要计算内容全部高度，才能确定在什么位置hidden
浮动的高度就要被计算进去，顺带达成了清理浮动的目标。
​	同理
​	overflow 非默认值
​	position 非默认值
​	float 非默认值
​	等都是遵循这个布局计算思路

## 6. HTTP2.0服务端推送

​		除了推送内嵌资源之外，还可以推送什么？其实, 任何HTTP响应都是平等的游戏。**我们能推送一次重定向吗? 是的, 那很简单:**

```js
spdy.createServer(options, function(req, res) {
  //push JavaScript asset (/newasset.js) to the client
  res.push('/newasset.js', {'content-type': 'application/javascript'}, function(err, stream) {
    stream.end('alert("hello from (redirected) push stream!")');
  });

  // push 301 redirect: /asset.js -> /newasset.js 这里为重定向
  res.push('/asset.js', {':status': 301, 'Location': '/newasset.js'}, function(err, stream) {
    stream.end('301 Redirect');
  });

  // write main response body and terminate stream
  res.end('<script src="/asset.js"></script>');
}).listen(443);
```

​		我们还能做的更多吗?推送缓存失效信息和再验证信息到客户端，怎么样？我们可以把已经存在于客户端缓存中的任何资源标记为失效（即推送一个失效时间戳），或者相反，推送一个携带未来时间戳的304来更新资源的生存期。**简言之，服务器能够主动的管理客户端缓存!**

## 7. SSE(Server-Sent Event)

​		SSE类似于长轮询的机制，但是它在每一次的连接中，不只等待一次数据的更动。客户端发送一个请求到服务端 ，服务端保持这个请求直到一个新的消息准备好，将消息返回至客户端，此时不关闭连接，仍然保持它，供其它消息使用。SSE的一大特色就是重复利用一个连接来处理每一个消息（又称event）。

```js
// 前端
var evtSource = new EventSource('/sse');
evtSource.addEventListener('testEvent', function (e) {
  console.log(e)
}
                           
// 后端
router.get('/sse', (req, res, next)=> {
  res.writeHead(200, {
    'Connection': 'keep-alive',
    'Content-Type': 'text/event-stream',	// 这个必须要设置
    'Cache-Control': 'no-cache'
  });
  let data = { meg: '这是一个测试SSE的数据内容' };
 
  setTimeout(() => {
    console.log(JSON.stringify(data));
    res.write('data:' + JSON.stringify(data) + '\n\n');
  }, 2000)
})
```

## 8. 内存泄漏怎么排查

​		chrome浏览器有一个memory选项卡，点击之后去进行快照，过一段时间再进行快照，展示快照之间的对比。

- \#New：新建了多少对象

- \#Deleted：回收了多少对象

- \#Delta：新建的对象个数减去回收的对象个数

  重点看closure（闭包），如果#Delta为正数，则表示创建了闭包函数，如果多个快照中都没有变负数，则表示没有销毁闭包。

## 9. 跨域如何携带cookie

​		使用CORS解决跨域问题，想要带上cookie必须要设置

- 前端：xhr.withCredentials = true 来标识传输cookie，如果使用axios，直接

  ```js
  axios({
      method: 'get',
      url,
      data,
      params,
      withCredentials: true
  })
  ```

- 后端需要设置响应头 Access-Control-Allow-Credentials：true 才能获得前端的cookie (注意：设为true时，Access-Control-Allow-Origin不能为'*')

## 10. etag解决了last-modefied什么问题

- 文件周期性修改，但是内容不变，此时我们并不希望客户端拉取这个资源；
- 修改过于频繁，秒级别以下的内容修改：last-modefied只能检查到秒级别的修改，如果1秒钟修改很多次，无法判断。
- 服务器不能精确得到文件的修改时间。

## 11. etag怎么生成

- 在nginx中：nginx 中 etag 由响应头的 Last-Modified 与 Content-Length` 表示为十六进制组合而成；
- hash算法

## 12. 登录验证的过程

1、数据库密码字段存入的数据为MD5加密后的数据

2、第一次访问登录页面通过uuid生成一个loginkey并存入session

3、前端提交登录数据的密码构成： MD5(MD5(用户填写的密码)+loginkey) 传入后台

4、后台验证用户存在的情况下取出用户数据库数据进行验证：MD5(数据库密码+session中的loginkey) === 用户提交的密码（步骤三传过来的密码）

## 13. MD5和SHA

- MD5和SHA-256都是密码散列函数，加密不可逆，所谓的解密实为暴力破解。
- MD5和SHA-256可以实现对任意长度对象加密，这就决定了传入参数类型为byte数组，如果为int或者其他类型，就不能满足上述要求，任何对象都可以被转成byte数组，为了保持通用性而这样设计。
- MD5和SHA-256都不能防止碰撞，只是SHA-256碰撞几率小于MD5，这也是MD5被逐渐抛弃的原因，除非被加密的数据并没有那么大的价值才会用MD5加密。

## 14. 断点续传

​		就是传输文件时，不必重头开始传输，而是从指定的位置继续传输，这样的功能就叫做断点续传。  断点续传的理解可以分为两部分：一部分是断点，一部分是续传。断点的由来是在传输过程中，将一个传输文件分成了多个部分，同时进行多个部分一起的下载，当某个时间点，任务被暂停了，此时下载暂停的位置就是断点了。续传就是当一个未完成的下载任务再次开始时，会从上次的断点继续传送。

​		**原理**：

- 其实断点续传的原理很简单,就是在Http的请求上多定义了断点续传相关的HTTP头 Range和Content-Range字段而已；
- 将文件分割为小部分，每次传输的时候带上索引。接收端拼接文件没这样还能进行文件的分片加密。

## 15. get可以有body，post可以用url传参

- 官方不推荐get请求带body，一般来说，服务端会忽略get请求的body，但是协商一下，更改一下配置是可以的。**往GET里加body会导致缓存机制失效。“GET 被设计来用 URI 来识别资源，如果让它的请求体中携带数据，那么通常的缓存服务便失效了，URI 不能作为缓存的 Key。”**

- Body类请求在语义能力上兼容url类请求，但是post用url传参不符合RESTful风格。而且body中能带的数据比url中大得多。

## 16. nginx如何实现跨域

- nginx实现CORS，在反向代理的时候，在响应头中加CORS头；
- 配置请求的转发，把一个域的请求转发到另一个域，这样浏览器就以为没有跨域；

## 17. 子域名不同的跨域

​		[www.a.baidu.com](http://www.a.baidu.com/) 与 [www.b.baidu.com](http://www.b.baidu.com/) 会发生跨域吗？

​		子域名不同，也会发生跨域。解决方法：

​		有a.ceshi.com通过iframe加载b.ceshi.com下的内容，通过对两个子域下的页面同时设置document.domian设置为相同的主域名可实现跨域读取数据：

## 18. cookie samesite属性

​		Chrome 51 开始，浏览器的 Cookie 新增加了一个`SameSite`属性，用来防止 CSRF 攻击和用户追踪。Cookie 的`SameSite`属性用来限制第三方 Cookie，从而减少安全风险。他有三个值：

- Strict：最为严格，完全禁止第三方 Cookie，跨站点时，任何情况下都不会发送 Cookie。换言之，只有当前网页的 URL 与请求目标一致，才会带上 Cookie。

- Lax：适当放宽，大多数情况也是不发送第三方 Cookie，但是导航到目标网址的 Get 请求除外。

- None：必须同时设置`Secure`属性（Cookie 只能通过 HTTPS 协议发送），否则无效。下面的设置有效：

  ```js
  Set-Cookie: widget_session=abc123; SameSite=None; Secure
  ```

## 19. meta标签

​		用来标识文档的元数据，元数据是用来描述数据的数据，它不会显示在页面中，但是机器却可以识别。因此meta标签常用来**定义页面的说明、关键字、最后修改日期等元数据**。这些元数据将，告诉浏览器怎么布局或重载页面，告诉搜索引擎页面的一些信息等。meta标签共有两个属性，分别是http-equiv属性和name属性。

- name属性：用于描述网页，比如关键词、描述，与之对应的属性是content。常见的name有keyword、description、viewport、robot、autor、copyright......

  ```html
  <meta name="参数" content="具体的描述">。
  ```

- http-equiv属性：顾名思义，相当于http的头文件使用，与之对应的属性也是content。常见的http-equiv有content-type设置网页字符集，便于浏览器解析（现在直接用charset属性）、cache-control指定缓存机制（和http一样）、expire、set-cookie、x-dns-prefetch-control（dns预解析）......**http响应头的优先级比meta高**。

  ```html
  <meta http-equiv="参数" content="具体的描述">
  ```

  **用js是可以获取到meta标签中值的**：

  ```js
  function getMeta(metaName) {
    const metas = document.getElementsByTagName('meta');
    for (let i = 0; i < metas.length; i++) {
      if (metas[i].getAttribute('name') === metaName) {
        return metas[i].getAttribute('content');
      }
    }
    return '';
  }
  ```

## 20. 物理像素、逻辑像素、CSS像素

- 物理像素：就是屏幕上的像素点个数；
- CSS像素：Web编程的概念，指的是css样式代码中的逻辑像素，一个CSS像素可能有多个物理像素来显示；
- 逻辑像素：为了抹去高分辨率屏幕所带来的适配问题，引入了这个概念，和CSS像素差不多；
- 像素密度（PPI  Pixels Per Inch）：每英寸的像素点个数（对角线）；
- 设备像素比（DPR Device Pixel Ratio）：物理像素 / 逻辑像素。

## 21. vue-router实现原理

- 实现一个install方法，把$router挂在Vue原型上用全局混入的方式，即Vue.mixin；
- 实现监听路由的变化，hash模式就监听window.onhashchange，history模式就监听window.onpopstate。
- 解析路由配置，能根据路由匹配到对应的组件；
- 实现两个全局组件router-link和router-view。

## 22. Promise做并发控制

- 使用 Promise.all 实现

```js
function fetchImageWithLimit(imageUrls, limit) {
  // copy一份，作为剩余url的记录
  let urls =[ ...imageUrls ]

  // 用来记录url - response 的映射
  // 保证输出列表与输入顺序一致
  let rs = new Map()

  // 递归的去取url进行请求
  function run() {
    if(urls.length > 0) {
      // 取一个，便少一个
      const url = urls.shift()
      return fetchImage(url).then(res => {
        rs.set(url, res)
        return run()
      })
    }
  }

  // 当imageUrls.length < limit的时候，我们也没有必要去创建多余的Promise
  const promiseList = Array(Math.min(limit, imageUrls.length))
    // 这里用Array.protetype.fill做了简写，但不能进一步简写成.fill(run())
    .fill(Promise.resolve())
    .map(promise => promise.then(run))
    
  return Promise.all(promiseList).then(() => imageUrls.map(item => rs.get(item)))
}
```

- 使用 Promise.race 实现：
  - 初始化`limit`个图片请求，作为`Promise.race`的参数
  - 当其中任一请求结束，即新建一个图片请求（还有的话），并连同之前未结束的`limit - 1`个图片请求一起作为新一轮的`Promise.race`的参数
  - 重复步骤2，直到图片都已被请求

## 23. HTTP1.1比1.0

- 缓存处理，HTTP1.1引入了Entity-tag、If-Unmodified-Since、If-Match、If-None-Match等更多可供选择的缓存头来控制缓存；
- 带宽优化和网络连接的使用：引入了 range 头域，它允许只请求资源的某个部分，返回响应码206（Partial Content），实现了断点续传；
- 新增了24个状态码；
- 增加 Host 头域：一台主机可能有多个虚拟主机，共享着一个IP地址，HTTP的Host头域来标志主机；
- 长连接和请求流水线（pipeline）：增加了 Connection: keep-alive，在一个tcp连接上传输多个HTTP请求。

## 24. HTTPS一定安全吗

​		https不一定全是安全的，一些网站会使用自签名证书。
​		自签名证书是指不受信任的任意机构或个人，使用工具自己签发的SSL证书。不是从公共证书机构（CA）那里申请来的证书，没有第三方监督审核，不受浏览器和操作系统信任，常被用于伪造证书进行中间人攻击，劫持SSL加密流量。

## 25. ES6的class和ES5的构造函数

- ES6定义的方法在原型链上，但是这些方法不可枚举；
- ES6中是严格模式，方法或类中的this本来是指向对象，单独使用指向undefined；
- ES6类由于是严格模式，不存在变量提升，保证子类父类的顺序；
- ES6中当函数用new关键词的时候，增加了new.target属性来判断当前调用的构造函数；
- ES6 class虽然是语法糖，但是不能不使用new直接调用；
- ES6 class必须先定义后使用，ES5可以先使用后定义。

## 26. Tree-Shaking、Webpack treeshaking是如何处理的

​		Tree shaking 是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 Dead code elimination。虽然 `tree shaking` 的概念在 1990 就提出了，但直到 `ES6` 的 `ES6-style` 模块出现后才真正被利用起来。因为 import 是完全静态的，因此可以进行静态分析，在编译的时候就知道哪些模块和变量未被使用。

## 27. Serverless

## 28. CDN

## 29. QUIC协议、HTTP3

- Quic概述：相比于 http2+tcp+tls 协议有如下优势：

  - 减少了 TCP 三次握手及 TLS 握手时间。
  - 改进的拥塞控制。
  - 避免队头阻塞的多路复用。
  - 连接迁移。
  - 前向冗余纠错。

  

## 30. TCP粘包问题

​		采用TCP协议进行网络数据传送的软件设计中，普遍存在粘包问题。这主要是由于现代操作系统的网络传输机制所产生的。我们知道，网络通信采用的套接字(socket)技术，其实现实际是由系统内核提供一片连续缓存(流缓冲)来实现应用层程序与网卡接口之间的中转功能。多个数据包被连续存储于连续的缓存中，在对数据包进行读取时由于无法确定发生方的发送边界，而采用某一估测值大小来进行数据读出，若双方的size不一致时就会使数据包的边界发生错位，导致读出错误的数据分包，进而曲解原始数据含义。

​		如何解决粘包问题：

- 约定固定长度的数据包传输：这种方式简单但是灵活性差，如果一个包长度不够，需要追加填充特殊字符；
- 以指定字符（串）作为包的结束标志：这种比较常见；
- 固定长度的包头+包体格式：包头大小固定，其中包含一个字段指定包体的长度。

## 31. bitmap、布隆过滤器

## 32. Vue-cli的常见优化

​		vue-cli依托于webpack，因此优化是基于webpack的。

- 配置某些包使用CDN，主要借助`html-webpack-plugin`这个插件以及`webpack externals`这个属性。
- 使用splitChunks进行代码分割：
  - 配置多入口可以进行代码分割，每个入口分一个；
  - 动态导入，使用  import  函数加载的模块将独立打包；
  - 使用`splitCode`插件

- 所有的库按需引入而不是全局引入

- 启用`gzip`压缩：gzip 是需要服务端配置的，当开启时会压缩我们的线上代码，但是如果我们前端已经使用 gzip 压缩过，那么服务端就会直接使用已经压缩好的代码，就不需要花时间再去压缩了。前端启用 gzip 压缩，使用到的插件是 `CompressionWebpackPlugin`；

- `DllPlugin` 插件 抽离第三方库的包，然后通过script 引入 ；

- 配置`webpack`压缩`js、css、img`等资源文件，分别有`uglifyjs-webpack-plugin`、`optimize-css-assets-webpack-plugin`。对图片可以直接用`url-loader`或者配合使用`image-webpack-loader`插件。


## 33. 常见的资源类型

​		HTTP header 字段中 Accept 表示客户端可支持的数据类型（Content-Type），如text/css, application/json， 这些类型都是以`MIME`规格定义的数据类型来表示的， 统称为`MIME`类型。

​		`MIME: Multipurpose Internet Mail Extensions`，多用途互联网邮件拓展。常见的`MIME`类型：text/plain，text/html，image/jpeg，image/png，audio/mpeg，audio/ogg，audio/\*，video/mp4，application/*，application/json。application/javascript，application/ecmascript，application/octet-stream。

​		`MIME`完整列表：https://www.iana.org/assignments/media-types/media-types.xhtml

## 34. THREEJS对象的拾取

- 一种是`raycast`射线拾取：是基于几何相交性计算的方式，从鼠标点击的地方，沿着视角方向发射一条射线。通过射线与三维模型的几何香蕉性来判断物体是否被拾取到。THREEJS内置了`Raycast`的类，提供一个射线，可以根据不同方向去发射射线，根据射线是否被阻挡来判断是否碰到了物体。
  - 一种是基于`GPU`的拾取方式：一般做法是给每个`mesh`一种颜色，然后绘制一遍，在鼠标所在的位置调用`readPicel`读取像素颜色，根据颜色和模型的对应关系，反推当前拾取的`Mesh`。
