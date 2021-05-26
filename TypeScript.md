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