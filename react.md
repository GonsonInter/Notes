# React学习

- react是什么：是一个将数据渲染为HTML视图的开源JavaScript库；
- 谁开发的：Facebook开发，且开源；
- 优点：
  - 原生JS操纵DOM繁琐，效率低；
  - 使用JS直接操作DOM会造成浏览器大量的重绘和重排；
  - 原生JS没有组件的化的编码方案，代码复用率低。



## React的特点

- 声明式：

  ​	你只需要描述UI(HTML)看起来是什么样的，就和写HTML一样，react负责渲染UI，并在数据变化时更新UI

- 基于组件（最重要的内容）

  ​	组件表示页面中的部分内容

- 学习一次，随处使用

  ​	使用react开发Web、移动端原生应用（react-native）、VR（react 360）等等应用。



## React的使用

- 安装react、react-dom

  ​	react包是核心，提供创建元素、组件等功能；react-dom提供了DOM相关功能。

- 引入react和react-dom两个js文件
- 创建react元素
- 渲染react元素到页面中



## React脚手架

- 脚手架的意义：
  - 脚手架是开发Web应用的必备；
  - 充分利用Webpack、Babel、ESLint等工具辅助开发；
  - react脚手架是零配置的，开箱即用；
  - 关注业务，而不是工具的配置。

- 使用React脚手架

  - 初始化项目，命令

    ```bash
    npx create-react-app <project-name>
    ```

  - 启动项目

    ```bash
    npm start
    # yarn start
    ```

    



## JSX

- createElement()的问题
  - 繁琐不简洁
  - 不直观，无法一眼看出所描述的结构
  - 不优雅，用户体验不佳

- JSX简介

  ​	JSX是JavaScript XML的简写，表示在JavaScript代码中写XML格式的代码。优势：声明式的语法更加直观，与HTML的结构相同，降低了学习成本，提升开发效率。JSX是React中的核心内容。

- 为什么脚手架中能够使用JSX语法
  - JSX不是标准的ECMAScript语法，他是ECMAScript的语法拓展；
  - 需要使用babel 编译处理之后，才能在浏览器环境下使用；
  - create-react-app脚手架中已经默认有该配置，无需手动配置；
  - 编译JSX语法的包为：@babel/preset-react。

- 注意点：

  - React元素的属性名使用驼峰命名法；
  - 特殊的属性名：class改为className、for改为forName、tabindex改为tabIndex；

  - 没有子节点的React元素可以使用单标签；
  - 推荐：使用小括号包裹JSX，从而避免JS中自动插入分号陷阱。

- JSX中使用JS表达式

  ```jsx
  const name = 'gsjt'
  const info = (
      <h1>hello JSX, { name }</h1>
  )
  ReactDOM.render(info, document.getElementById('root'));
  ```

- 列表渲染，注意key的使用，不写key可以渲染，但是会报错

  ```jsx
  const songs = [
    { id: 1, name: 'name1' },
    { id: 2, name: 'name2' },
    { id: 3, name: 'name3' },
  ]
  const list = (
      <ul>
        { songs.map(item => <li key={ item.id }>{ item.id }-{ item.name }</li>) }
      </ul>
  )
  ReactDOM.render(list, document.getElementById('root'));
  ```

- JSX样式处理
  - 行内样式：style属性，它是一个对象，其中属性使用驼峰命名；
  - 使用className，使用样式文件并导入。



## 组件基础

- 组件介绍：
  - 组件是react的一等公民，使用react就是在使用组件；
  - 组件表示页面中的部分功能；
  - 组合多个组件实现完整的功能页面；
  - 特点：可复用、独立、可组合。

- 创建组建的两种方式：

  - 函数组件：使用JS的函数（或箭头函数）创建的组件。

  > 约定一：函数名必须以**大写字母开头**，react据此区分组件和普通的react元素；
  >
  > 约定二：函数组件**必须有返回值**，表示该组件的结构,不写返回值报错；
  >
  > 如果返回值是null，则不渲染任何内容。

  ```jsx
  function Hello() {
      return (
          <div> Hello组件 </div>
      )
  }
  const Hello = () => <div> Hello组件 </div>
  ```

  - 类组件：使用ES6中的class来创建组件

  > 约定一：类名称必须以大写字母开头；
  >
  > 约定二：类组件应该继承React.component父类，从而可以使用父类中提供的额方法或属性；
  >
  > 约定三：类组件必须提供**render()**方法；
  >
  > 约定四：render()方法必须有返回值，表示该组件的结构。

- 组件模块化：将组件抽离到单独的JS文件中；
  - 创建组件文件Hello.js;
  - 在文件中导入react；
  - 编写组件代码；
  - 导出；
  - 在其他文件中导入组件。



## 事件处理

- 事件绑定：React的事件绑定语法和原生DOM事件的语法很相似；
- 语法：on + 事件名称 = { 事件处理程序 }，比如：onclick = { () => {} }；
- 注意：React事件采用**驼峰命名法**，如onMouseEnter、onFocus。

```jsx
class App extends React.Component {
  handleClick() {
    console.log('单击事件触发了');
  }
  render() {
    return <button onClick={ this.handleClick }>点我</button>
  }
}
```

- 事件对象：
  - 可以通过事件处理函数的参数获取事件对象event；
  - React中的事件对象叫做：合成事件（对象）；
  - 合成事件：兼容所有浏览器，无需担心跨浏览器的兼容性问题。



## 有状态组件和无状态组件

- 函数组件又叫做**无状态组件**，类组件又叫做**有状态组件**；
- 状态（state）即**数据**；

- 函数组件没有自己的状态，因此**只负责数据的静态展示**；
- 类组件有自己的状态，**负责更新UI**，让页面动起来。



## state和setState

- state的基本使用：

  - 状态（state）即数据，是组件内部的私有数据，只能在组件内部使用，可以在组件的构造函数中初始化state对象，也可以直接写成员变量；
  - **state值是对象**，表示一个组件中可已有多个数据；

  - 获取状态：this.state

- setState() 修改状态
  - 状态是可变的
  - 语法为：this.setState({ 要修改的数据 })
  - 注意：**不要直接修改state中的值，这是错误的！**
  - setState的作用：1、修改state       2、更新UI
  - 思想：**数据驱动视图**

```jsx
/**
 * 计数器组件
 */
class Counter extends React.Component {
  /**
   * 构造函数方式
   */
  // constructor(props) {
  //   super(props);
  //   this.state = {
  //     count: 0
  //   }
  // }
  /**
   * 成员变量方式
   */
  state = {
    count: 0
  }

  render() {
    return (
        <div>
          <h1>计数器：{ this.state.count }</h1>
          <button onClick={ () => {
            this.setState({
              count: this.state.count + 1
            })
          } }> + 1 </button>
        </div>
    )
  }
}
```



## 受控组件和非受控组件

- 受控组件：在HTML中，标签<input>、<textarea>、<select>的值的改变通常是根据用户输入进行更新。在React中，可变状态通常保存在组件的状态属性中，并且只能使用 setState() 更新，而呈现表单的React组件也控制着在后续用户输入时该表单中发生的情况，以这种由React控制的输入表单元素而改变其值的方式，称为：“受控组件”。
- 非受控组件：表单数据由DOM本身处理。即`不受setState()`的控制，与传统的HTML表单输入相似，input输入值即显示最新值（使用 `ref`从DOM获取表单值）。



## 组件通讯

​		组件是封闭的单元，默认情况下，只能使用组件自己的数据，在组件化的过程中，我们将一个完整的功能拆分为多个组件，以更好的完成整个应用的功能。而在这个过程中，多个组件之间不可避免需要共享某些数据。为了实现这些功能，就需要打破组件的封闭性，让其与外界沟通。

- 组件的props：组件是封闭的，想要接受外部的数据应该通过props来实现。

  - props的作用：接受传递给组件的数据；
  - 传递数据：给组件的标签添加属性；
  - 接收数据：函数组件通过参数props接收数据，类组件通过this.props接受数据。

  ![image-20210630205712670](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210630205712670.png)

- props的特点：
  - 可以给组件传递任意类型的数据；
  - **props是只读的**，修改会报错；
  - 注意：使用类组件的时候，如果写了构造函数，应该将props传递给super()，否则无法在构造函数中获取到props。

- 父组件传值给子组件：使用props。
- 子组件传值给父组件：父组件用props传给子组件一个回调函数，子组件去调用回调函数，并把传值作为参数。需要注意，回调函数的this指向问题。

- 兄弟组件的通讯：将共享状态提升到最近的公共父组件中，由公共父组件来管理这个状态。公共父组件不仅要提供共享状态，而且要提供操作共享状态的方法。需要通讯的子组件只需要通过props接受状态或操作状态的方法。



## Context

**作用：跨组件传递数据（比如：主题、语言等）**

- 使用步骤：

  - 调用React.createContext()创建 Provider（提供数据）和 Consumer（消费数据）两个组件；

    ```jsx
    const { Provider, Consumer } = React.createContext();
    ```

  - 使用Provider组件作为父节点：

    ```jsx
    <Provider>
        <div className='App'>
        	<Child />
        </div>
    </Provider>
    ```

  - 设置value值，表示要传递的数据；

  - 调用Consumer组件接收数据：

    ![image-20210630213455240](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210630213455240.png)



## props深入

- children属性：当组件具有子节点的时候，会自动加上children属性。children属性只是一个特殊的属性而已。双标签内的内容就是children，可以是任意类型。

- **props校验**：对于组件来说，props是外来的，无法保证组件的使用者传入什么数据。如果传入的数据格式不对，可能导致组件内部报错。关键问题是组建的使用者不知道错误的明确原因。

  - props校验允许在创建组件的时候就指定props的类型、格式等；
  - 作用是捕获使用组件时因为props导致的错误，给出明确的错误提示，增加组件的健壮性。

  - 使用步骤：
    - 安装包 prop-types （npm/yarn）;
    - 导入prop-types包；
    - 使用**组件名.propTypes = {}**给组件的props添加校验规则；
    - 校验规则通过PropTypes对象来指定。

![image-20210701102630888](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701102630888.png)

- - 常见的约束规则：

    ![image-20210701103207184](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701103207184.png)

    全部类型可查文档：https://reactjs.org/docs/typechecking-with-proptypes.html



## 组件的生命周期

> 概述：
>
> 	- 意义：组件的生命周期有助于我们理解组件的运行方式，完成更复杂的组件功能，分析组件的错误原因等。
> 	- 组件的生命周期：组件从创建挂载载到页面中运行，再到组件不用时被卸载的过程。
> 	- 生命周期的每个阶段总是伴随着一些方法的调用，这些方法就是生命周期的钩子函数。
> 	- 钩子函数为开发人员在不同阶段操作组件提供了时机。
> 	- **只有类组件才有生命周期**，因为函数组件没有状态，是静态的。

![image-20210701111108802](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701111108802.png)

- 创建挂载时期：

  ![image-20210701111631352](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701111631352.png)

- 组件运行时期：

  - 更新执行的时机：1.setState	2.forceUpdate	3.组件接收到新的props
  - 以上三种任意变化组件就会重新渲染。

  ![image-20210701111933928](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701111933928.png)

- 组件卸载时期：

  - 执行时机：组件从页面消失。

  ![image-20210701112116071](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701112116071.png)

- 一些不常用的钩子函数：

![image-20210701112304171](C:\Users\zijiewang\AppData\Roaming\Typora\typora-user-images\image-20210701112304171.png)

