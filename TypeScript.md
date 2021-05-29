# TypeScript

## 1. 数据类型

- number数字类型

- string字符串类型

- boolean布尔类型

- array数组类型

  ```typescript
  let arr:number[] = [1, 2, 3.1, 12.3];
  let arr2:Array<number> = [];
  ```

- tuple元组类型，属于数组的一种

  ```typescript
  let t:[string, number, boolean] = ['ts', 3.14, false];
  ```

- enum枚举类型，如果枚举类型中元素不赋值，则默认为元素的索引值

  ```typescript
  enum Flag {
      success = 1,
      error = -1
  };
  let f:Flag = Flag.error;
  console.log(f);		// -1
  ```

- any任意类型，通常可当作对象类型使用，因为ts没有Object类型

- null和undefined

  ```typescript
  // 一个元素可能是number类型，可能是null类型，可能是undefined类型
  let num:number | null | undefined;
  ```

- void类型，空类型，一般用于定义方法且方法没有返回值的时候

- never类型，表示永远不可能有值的类型，如果一个函数永远只抛出异常或者永远不会结束或者存在不可能返回的情况，则为never类型



## 2. 函数

### （1）函数定义

- 函数声明式

  ```typescript
  function fun:string {
      return 'run';
  }		// 返回值的类型错误会报错
  ```

- 函数表达式

  ```typescript
  let fun = function():string {
      return 'run';
  }
  ```

### （2）函数传参

```typescript
function getInfo(name:string, age:number):string {
    return `${name} --- ${age}`;
}
console.log(getInfo('张三', 20));

// 没有返回值的方法
function run():void {
    console.log('run');
}
run();
```

- 方法可选参数，在ES5中，方法的实参和形参可以不一样，但是在**TS中必须一样**，否则就需要配置可选参数。加入 ? 的参数是可选参数。**注意：可选参数必须配置到参数的最后面**

  ```typescript
  function getInfo(name:string, age?:number):string {
      if (age) {
          return `${name} --- ${age}`;
      }	else {
          return `${name} --- 年龄保密`;
      }
  }
  ```

- 设置默认参数，在ES5中不能设置默认参数，但是在ES6和TS中可以设置默认参数。

  ```typescript
  function getInfo(name:string, age:number = 20):string {
      return `${name} --- ${age}`;
  }
  ```

- 剩余参数：

  ```typescript
  function sum(a:number, b:number, c: number, d:number):number {
      return a + b + c + d;
  }
  
  // 转化为以下的写法
  function sum(...result:number[]):number {
      let sum = 0;
      for (let i = 0; i < result.length; i ++) {
          sum += result[i];
      }
      return sum;
  }
  ```

- 函数重载，同名的函数可以接受不同的参数做不同的事情。在ES5中，如果出现同名的方法，则下面的会替换掉上面的。

  ```typescript
  function getInfo(name:string):string;
  function getInfo(age:number):string;
  function getInfo(str:any):any {
      if (typeof str === 'string') {
          return '我是' + str;
      }	else {
          return '我的年龄是' + str;
      }
  }
  console.log(getInfo('张三'));
  console.log(getInfo(20));
  console.log(getInfo(true));		// 报错
  ```



## 3. 类

```typescript
class Person{
    name:string;	// 前面省略了public关键字
    constructor(name:string) {		// 构造函数  实例化类的时候触发方法
        this.name = name;
    }
    getName():string {
        return this.name;
    }
    setName(name:string):void {
        this.name = name;
    }
    run():void {
        console.log(this.name);
    }
}
let p = new Person('张三');
console.log(p.getName());
```

- 实现继承	extends、super

```typescript
class Web extends Person {
    constructor(name:string) {
        super(name);
    }
    work() {
        console.log(`${this.name}在运动`);
    }
}

let w = new Web('李四');
console.log(w.run());
```

- 类元素修饰符
  - public
  - protected
  - private

- 静态方法 static

  静态方法可以被继承，也可以修改。

- 抽象类和抽象方法
  - 抽象方法只能存在于抽象类；
  - 抽象类不一定由抽象方法；
  - 抽象类不能直接被实例化，抽象类中的抽象方法不包含具体实现；
  - 抽象类的子类必须实现抽象类里的抽象方法，抽象类和抽象方法用来定义标准。



## 4. 接口

> ​	接口的作用：在面向对象语言中，接口是一种规范的定义，它定义了行为和动作的规范，在程序设计里面，接口起到了一种限制和规范作用。接口不关心这些类内部的状态数据，也不关系方法里的实现细节，它只规定这批类里面必须要提供的某些方法，提供这些方法的就可以满足实际需求。typescript中的接口类似于java，同时还增加了更灵活的接口类型，包括属性、函数、可索引和类等。

- **属性接口**，对对象进行约束。

  ```typescript
  // 属性接口，对传入的对象进行约束
  interface FullName {
      firstName:string;   // 必须存在的属性
      secondName?:string;		// 可选的属性
  }
  function printName(name:FullName):void {
      // 必须传入对象  firstName  可传入对象  secondName
      console.log(name.firstName + '---' + name.secondName);
  }
  ```

- **函数类型接口**，对方法传入的参数和返回值进行约束

  ```typescript
  interface encrypt {
      (key:string, value:string):string;
  }
  let md5:encrypt = function(key:string, value:string):string {
      return key + value;
  }
  ```

- **可索引接口**，对数组、对象的约束（不常用）

  ```typescript
  // 对数组的约束
  interface UserArr {
      [index:number]:string
  }
  let arr:UserArr = ['aaa', 'bbb'];
  console.log(arr[0]);
  
  // 对对象的约束
  interface UserObj {
      [index:string]:string
  }
  let arr:UserObj = {
      name: 'gsjt'
  }
  ```

- **类类型接口**，对类的约束，和抽象类有点相似

  ```typescript
  interface Animal {
      name:string;
      eat(str:string):void;
  }
  class Dog implements Animal {
      name:string;
      constructor(name:string) {
          this.name = name;
      }
      eat() {
          console.log(this.name + '吃东西');
      }
  }
  ```



## 5. 接口的拓展：接口继承接口



## 6. 泛型

- 泛型定义和泛型函数

  ```typescript
  function getData<T>(value:T):T {
      return value;
  }
  getData<number>(123)
  ```

- 泛型类

  ```typescript
  class MinClass<T> {
      public list:T[] = [];
      add(num:T):void {
          this.list.push(num);
      }
      min():T {
          return this.list.reduce((last, item) => {
              return Math.min(last, item);
          }, Number.MAX_VALUE);
      }
  }
  ```

- 泛型接口，要实现泛型接口，这个类也必须是一个泛型类

  ```typescript
  // 第一种定义方法
  interface ConfigFn2{
  	<T>(value:T):T;
  }
  
  var getData:ConfigFn2=function<T>(value:T):T{
  	return value
  }
  getData<string>('alex') // alex
  getData<number>(911) // 911
  
  // 第二种定义方法
  interface ConfigFn<T>{
  	(value:T):T
  }
  function getData<T>(value:T):T{
  	return value
  }
  var myGetData:ConfigFn<string>=getData
  mygetData('abc')
  ```



## 7. 模块

> ​		TypeScript 1.5里的术语名称发生了变化。“内部模块”现在简称命名空间，“外部模块”现在简称模块。

​		使用 export 和 import，和ES6模块化是相同的。导入 ts 文件，不加后缀名，因为编译后是 js 文件，没有 .ts 。



## 8. 命名空间

> ​		和模块的区别：
>
> 			- 命名空间：内部模块，主要用于组织代码，避免命名冲突；
> 			- 模块：ts的外部模块的简称，侧重代码的复用，一个模块里可能会有多个命名空间。



## 9. 装饰器

> ​		装饰器是一种特殊类型的声明，它能够被附加到类声明、方法、属性或参数上，可以修改类的行为。修饰器对类的行为的改变，是代码编译时发生的（不是TypeScript编译，而是js在执行机中编译阶段），而不是在运行时。这意味着，修饰器能在编译阶段运行代码。也就是说，修饰器本质就是编译时执行的函数。

### （1）类装饰器

- 普通装饰器，不可以传参

  ```typescript
  // 类装饰器
  function logClass(params:any) {
      console.log(params);
      // params就是被装饰的那个类
      params.prototype.apiUrl = '动态扩展的属性';
      params.prototype.run() {
          console.log('动态扩展的方法');
      }
  }
  
  @logClass
  class HttpClient() {
      constructor() {}
  }
  
  let http:any = new HttpClient();
  console.log(http.apiUrl);
  ```

- 装饰器工厂，可以传参

  ```typescript
  // 类装饰器
  function logClass(params:string) {
      return function(target:any) {
          // target就是被装饰的类
          console.log(target);
          console.log(params);
          target.prototype.apiUrl = params;
      }
  }
  
  @logClass('http:www.baidu.com/api')
  class HttpClient() {
      constructor() {}
  }
  
  let http:any = new HttpClient();
  console.log(http.apiUrl);
  ```

### （2）属性装饰器

​		属性装饰器表达式会在运行时当作函数被调用，传入两个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员来说是类的原型对象；

2. 成员的名字；

   ```typescript
   function logProp(params:any) {
       return function(target:any, attr:any) {
           console.log(target)  // { constructor:f, getData:f } 
           console.log(attr)  // url
           target[attr] = params;  //通过原型对象修改属性值 = 装饰器传入的参数
           target.api = 'xxxxx';  //扩展属性
           target.run = function() {  //扩展方法
               console.log('run...');
           }
       }
   }
   class HttpClient {
       @logProp('http://baidu.com')
       public url:any|undefined;
       constructor() { }
       getData() {
           console.log(this.url);
       }
   }
   var http:any = new HttpClient();
   http.getData();  // http://baidu.com
   console.log(http.api);  // xxxxx
   http.run();  // run...
   ```

### （3）方法装饰器

​		他会被应用到方法的属性描述上，可以用来监视，修改或者替换方法定义。方法装饰器会在运行的时候传入下列三个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象；

2. 成员的名字；

3. 成员的属性描述符。

   ```typescript
   // 方法装饰器
   function logMethod(params:any) {
       return function(target:any, method:any, desc:any) {
           console.log(target);
           console.log(method);
           console.log(desc);
           /* 修改被装饰的方法 */
           //1. 保存原方法体
           let oldMethod = desc.value;
           //2. 重新定义方法体
           desc.value = function(...args:any[]) {
               //3. 把传入的数组元素都转为字符串
               let newArgs = args.map((item)=>{
                   return String(item);
               });
               //4. 执行原来的方法体
               oldMethod.apply(this, newArgs);
           }
       }
   }
   
   class HttpClient {
       constructor() { }
       @logMethod('http://baidu.com')
       getData(...args:any[]) {
           console.log('getData: ', args);
       }
   }
   ```

### （4）方法参数装饰器

​		方法参数器表达式会在运行时仿作函数被调用，传入下列3个参数：

1. 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象；
2. 参数的名字；
3. 参数在函数参数列表中的索引。

### （5）装饰器的执行顺序

​		**属性装饰器 > 方法装饰器 > 方法参数装饰器 > 类装饰器**

​		如果有相同类型的装饰器，则执行顺序是从后往前。





# Vue 3.x

