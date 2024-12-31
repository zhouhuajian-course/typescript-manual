# TypeScript Manual

https://www.typescriptlang.org/

1. 调试 ts 代码步骤
```
① 如果没有全局安装 typescript ，全局安装 npm install -g typescript 报错可以--force
② tsc --init 初始化
③ 创建 index.ts，写一行代码 `console.log(123)`
④ tsc --watch
⑤ 创建 index.html，引入 index.js，使用 Live Server 打开，打开 开发者工具 控制台
```
2. ts 装饰器 `https://www.typescriptlang.org/docs/handbook/decorators.html`
3. js 有两个特别的值 null 和 undefined
4. 非空断言操作符 （后缀!）`non-null-assertion-operator` `https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#non-null-assertion-operator-postfix-`
```typescript
function getUserIntro(name: string | undefined) {
  if (name == undefined) {
    name = 'Joe'
  }
  return `用户名字叫：${name.toLocaleUpperCase()}`
}

console.log(getUserIntro(undefined));

function getUserIntro2(name: string | undefined) {
  const setDefaultName = function() {
    if (name == undefined) {
      name = 'Joe'
    }
  }
  setDefaultName()
  // 这里会报错，index.ts:24:19 - error TS18048: 'name' is possibly 'undefined'.
  // "strictNullChecks": false,        strictNullChecks默认为true，如果设置为false，则不报错         
  // return `用户名字叫：${name.toLowerCase()}`

  // 因为你知道 name 不可能为 undefiend 所以加个!，也能解决问题
  return `用户名字叫：${name!.toLowerCase()}`
}

console.log(getUserIntro2(undefined));

// 注意这个例子，类型静态检查时和编译时，没有报错，但name还是可能是undefined，最后编译没错，但是运行时出错了
// Uncaught TypeError: Cannot read properties of undefined (reading 'toLowerCase')
function getUserIntro3(name: string | undefined) {
  return `用户名字叫：${name!.toLowerCase()}`
}

console.log(getUserIntro3(undefined));
```
结论：非空断言操作符(!)告诉TypeScript编译器，某个变量在运行时不会是 null 或 undefined。这在编译器无法确定变量非null非undefined的情况下非常有用。可以告诉编译期，这个变量不可能为 null 或 undefined，从而避免编译错误。但非空断言操作符有一定风险，实际上 变量 可能为 null 或 undefined，使用这个操作符可能导致运行时错误。使用这个非空断言操作符，需要对变量有充分的了解和控制。这个操作符很强大，可以帮助我们在编译时避免不必要的 null 和 undefined 检查，使用时要谨慎，确保不会导致运行时错误。通过合理使用非空断言操作符，可以编写更简洁、更安全的 TypeScript 代码。  
5. `    // "strictPropertyInitialization": true,             /* Check for class properties that are declared but not set in the constructor. */`  
严格的属性初始化。  
在JS中，类属性可以声明后，不用初始化，例如
```javascript
  <script>
    class Student {
      name
      age
    }
    console.log(new Student());
  </script>
/*
Student
  age: undefined
  name: undefined
  [[Prototype]]: Object
*/
```
但在TS中，2.7 版本之后，有一个新的编译器选项 strictPropertyInitialization，默认为 true，编译器会确保 class 类中的所有属性和方法都已经初始化，如果没有，那么编译时会报错  
`Property 'name' has no initializer and is not definitely assigned in the constructor.ts(2564)` 
    
解决方案：  
① 设置strictPropertyInitialization: false 不建议
② constructor 在构造函数，明确给属性赋值。缺点代码比较臃肿，new 对象的时候必须传对应参数
```typescript
class Student {
  name: string
  age: number
  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }
}
console.log(new Student('Joe', 18));
```
③ 直接给属性默认值。代码更简洁
```typescript
class Student {
  name: string = ''
  age: number = 0
}
console.log(new Student());
```
④ 告诉 编译器 这个属性可能为 undefined。缺点是后续操作比较麻烦，要时刻考虑 undefined 的问题
```typescript
class Student {
  name: string | undefined
  age: number | undefined
}
console.log(new Student());
```
⑤ 告诉 编译器 这个属性不可能为 undefined，虽然它的实际值，可以是 undefined。缺点是属性真的有可能是undefined，后续操作会报错，但好处是，如果你确定它不会是undefined，后续操作的代码会比较简洁。
```typescript
class Student {
  name!: string 
  age!: number
}
console.log(new Student());
```
6. 当 TypeScript 转换为 JavaScript 时，接口 interface 会消失，因为 JavaScript 没有接口 interface。
